# 链上 Indexer 工程：Meme 平台的真正护城河

> 你以为 Meme 平台的护城河是 UI 或路由？错。是**indexer** —— 把每秒几千笔原始链上数据变成可查询信号的工程系统。头部平台每月烧几万美元在这上面。

## 表面认知

"看链上数据不就是订阅 WebSocket 吗？" 是也不是。订阅 WebSocket 只是第一步，做出一个能支撑**百万用户 + 实时查询 + 历史回溯**的系统是**完全不同的工程量级**。

## 真实情况

### Indexer 要解决的核心问题

```
原始输入                          可用输出
─────────                         ─────────
每秒 3000+ 笔 Solana 交易    →    "这个 Token 过去 5 分钟涨了 +350%"
每笔交易 10+ 账户更新        →    "0x...abc 是 Smart Money（胜率 65%）"
不同 DEX 的事件格式不同      →    "GMGN 用户 24h 交易量 $1.2M"
偶尔的区块 reorg            →    "新币发射后 30 秒内 Top 10 买家是谁"
```

把左边变成右边，就是 indexer 工程。

### Indexer 的分层架构

所有头部 Meme 平台的 indexer 都大致是这个结构：

```
┌────────────────────────────────────────────────────┐
│  Layer 1: 数据采集（Ingestion）                     │
│  - Solana: Geyser / Yellowstone gRPC / ShredStream │
│  - EVM: WebSocket subscribe / Alchemy / Helius     │
│  - 专业 RPC 集群（自建 + 多家冗余）                   │
└────────────────────────────────────────────────────┘
                    ↓ (raw events)
┌────────────────────────────────────────────────────┐
│  Layer 2: 解析与丰富（Parse & Enrich）              │
│  - 解码 Anchor/Solidity 指令                        │
│  - 识别 Swap / Transfer / LP 操作                    │
│  - 关联 Token metadata、Oracle 价格                 │
│  - 计算 USD 价值、滑点、Gas 成本                     │
└────────────────────────────────────────────────────┘
                    ↓ (structured events)
┌────────────────────────────────────────────────────┐
│  Layer 3: 事件路由与去重（Routing & Deduping）       │
│  - Kafka / Pulsar 消息队列                          │
│  - 处理 reorg（回滚 + 重新索引）                      │
│  - 幂等性保证（同一 tx 不会索引两次）                  │
└────────────────────────────────────────────────────┘
                    ↓ (canonical events)
┌────────────────────────────────────────────────────┐
│  Layer 4: 存储（Storage）                           │
│  - 热存储：Redis（最近 24h K 线、当前持仓）           │
│  - 温存储：ClickHouse / TiDB（过去 30 天数据）        │
│  - 冷存储：S3 + Parquet（历史数据）                   │
└────────────────────────────────────────────────────┘
                    ↓ (queryable data)
┌────────────────────────────────────────────────────┐
│  Layer 5: 查询与聚合（Query & Aggregation）         │
│  - 实时聚合（1m/5m/1h K 线）                        │
│  - Smart Money 评分                                 │
│  - 相似 Token 推荐                                   │
│  - 告警（Rug Pull / 大户买入等）                     │
└────────────────────────────────────────────────────┘
                    ↓
┌────────────────────────────────────────────────────┐
│  Layer 6: API / WebSocket 服务                      │
│  - REST API（查询历史）                              │
│  - WebSocket 推送（实时更新）                        │
│  - GraphQL（复杂组合查询）                           │
└────────────────────────────────────────────────────┘
```

### Layer 1：数据采集的真实挑战

**Solana 的数据采集演化**：

| 时代 | 方案 | 延迟 | 完整性 |
|------|------|-----|--------|
| 2022 | WebSocket 订阅 RPC | 1-3s | 低（会丢事件） |
| 2023 | Geyser 插件（本地节点） | 300-500ms | 高 |
| 2024 | **Yellowstone gRPC**（Triton 开源） | 100-300ms | 高 |
| 2025 | ShredStream（Solana 底层 shred） | **<50ms** | 最高 |

**Yellowstone gRPC 成为事实标准**：
- 大部分头部平台（Helius、Shyft、自建）都用 Yellowstone gRPC
- 可以订阅账户变化、交易、slot 更新
- 单条连接能跟上 Solana 全链流量

**ShredStream 是下一代**：
- 直接从 validator 的 shred（区块分片）层订阅
- 比 gRPC 更早看到新交易（leader 还没 finalize）
- 成本 $10K-$50K/月（相比 gRPC 的 $2K-$10K 更贵）

**EVM 的数据采集对比**：

```
Alchemy / QuickNode / Infura WebSocket
    ↓
过滤 Swap 事件（特定 DEX 的合约地址）
    ↓
订阅 pending 交易（可选，用于 MEV 监控）
```

- 延迟：500ms-2s（慢于 Solana）
- 成本：相对低（EVM 交易量少）
- 挑战：事件格式因 DEX 而异，PancakeSwap V2 vs V3 vs Uniswap V4 都不同

### Layer 2：解析的工程量

**为什么解析这么复杂**：

每个 DEX 的 Swap 事件结构都不一样：

```rust
// Raydium V4 的 swap 指令
accounts: [
    pool_state,       // 池子状态
    amm_authority,    // 权限账户
    user_source_ata,  // 用户输入 Token 账户
    user_dest_ata,    // 用户输出 Token 账户
    // ... 还有 15 个其他账户
]

// Orca Whirlpool 的 swap
accounts: [
    whirlpool,
    token_owner_account_a,
    token_owner_account_b,
    // ... 结构完全不同
]
```

**每个 DEX 都需要专属解析器**。头部平台支持 20+ DEX = **20+ 套解析逻辑**。

**解析器需要识别的事**：
- 谁 swap 了（from address）
- 哪两个 Token（mint_in, mint_out）
- 数量（amount_in, amount_out）
- 滑点（预期 vs 实际）
- 费用分配（Platform fee、LP fee、Referral）

**边界情况**：
- 部分 fill（复杂指令中途失败）
- 多跳 swap（一笔交易经过多个 DEX）
- Flashloan（借入还回同一笔交易）

### Layer 3：Reorg 处理（最容易写错的地方）

**Solana 的 reorg 特征**：

- 很少发生（<0.1% 的 slot）
- 但一旦发生，可能影响最近几十个 slot
- `processed` vs `confirmed` vs `finalized` 三层状态

**正确的处理方式**：

```
1. 把所有事件标记为 "未确认"（status=pending）
2. 等待 confirmed (≈ 2 秒) → 升级为 "已确认"
3. 等待 finalized (≈ 13 秒) → 升级为 "最终"
4. 如果 reorg 了:
   - 回滚所有 pending / confirmed 但不在新链的事件
   - 重新索引新链上的事件
```

**常见错误**：直接用 `processed` 状态索引，reorg 发生时数据库里有脏数据。

**EVM 的 reorg 处理**：

- BSC / ETH 主网 reorg 更少（finalized 约 2 分钟）
- 但 L2（Base / Arbitrum）有 batch 提交延迟，需要特殊处理
- 通用做法：等 12 个区块确认后再写入"最终"状态

### Layer 4：存储分层的艺术

**为什么要分层**：

- 热数据查询：毫秒级响应（"这个 Token 当前价格"）
- 温数据查询：秒级响应（"过去 24h K 线"）
- 冷数据查询：分钟级响应（"这个钱包历史所有交易"）

一个数据库做不了所有事。

**典型分配**：

| 数据类型 | 存储 | 保留期 | 查询模式 |
|---------|------|-------|---------|
| 当前 Token 状态 | Redis | 7 天 | 点查询 |
| 实时 K 线（1m、5m） | Redis + TimescaleDB | 30 天 | 范围查询 |
| 历史交易明细 | ClickHouse / TiDB | 90 天 | 复杂聚合 |
| 钱包持仓快照 | PostgreSQL + Redis cache | 永久 | 按地址查询 |
| 长期归档 | S3 + Parquet | 永久 | 批量分析 |

**GMGN 使用 TiDB 的原因**（公开披露）：
- 跨链数据 + 大表 + 高并发 → 单机 MySQL 扛不住
- 分库分表 → 实时聚合查询变难
- **TiDB 的 HTAP 特性**：既能 OLTP 又能 OLAP
- 成本约降 50%（对比之前的 MySQL 集群）

### Layer 5：聚合计算的复杂度

**实时 K 线计算**：

简单想法："新交易来了，更新当前分钟的 K 线"。

实际问题：
- 一个 Token 同时在多个 DEX 有池子 → 加权平均
- 交易乱序到达（网络延迟） → 需要 re-compute
- 跨链同步（跨链桥 transfer）→ 难以精确计价

**Smart Money 评分**：

"历史胜率高的钱包"。说起来简单，做起来需要：

```
Step 1: 追踪每个钱包的每笔交易
Step 2: 关联每个 Token 的进场/出场价
Step 3: 计算单笔 PnL
Step 4: 聚合所有交易的胜率、夏普比率
Step 5: 按时间加权（近期表现更重要）
Step 6: 过滤 wash trading（自己对自己交易）
```

**一个钱包的 Smart Money 评分，可能需要扫描过去 6 个月的 **几千笔交易**。对千万级钱包做这件事 → **PB 级数据 + 持续计算集群**。

### Layer 6：API 层的性能挑战

**高频查询场景**：

- 10 万用户同时打开 Token 页面
- 每个页面每 5 秒拉一次价格 + K 线 + 交易量
- = 每秒 20,000+ 查询

**优化手段**：

1. **Redis 缓存**：热 Token 的所有数据都在内存
2. **WebSocket 推送**：避免客户端轮询
3. **CDN 缓存静态响应**：Token 元数据、历史 K 线
4. **数据分片**：按 Token 地址哈希分布到不同节点

**头部平台的 API 容量**：

- GMGN 高峰：**50,000 req/s**
- Axiom 高峰：**30,000 req/s**
- 这是 CEX 级别的负载

### 成本估算（月度）

一个支持多链 + 百万用户的 Meme 平台 indexer：

| 项 | 成本 |
|-----|------|
| 专业 RPC 节点（Solana + EVM） | $30K-$100K |
| Yellowstone gRPC / ShredStream | $5K-$50K |
| TiDB / ClickHouse 集群 | $10K-$40K |
| Redis 集群 | $5K-$20K |
| S3 + 数据传输 | $2K-$10K |
| 服务器（解析、聚合、API） | $20K-$80K |
| 监控与日志（Datadog/Grafana） | $5K-$20K |
| 团队（5-10 名后端 + DevOps） | $100K-$300K |
| **合计（月）** | **$177K-$620K** |

每年 $2M-$7M，这是头部平台的"入场费"。

### 常见失败模式

**1. 单点故障**

RPC 集群挂了 → 所有数据中断。对策：多家 RPC 冗余 + 自动切换。

**2. 数据延迟悄悄积累**

解析慢 → 队列堆积 → 前端看到的数据慢几分钟。对策：监控 P99 延迟 + 自动扩容。

**3. Reorg 引起脏数据**

前面讲过。对策：严格按三层状态索引。

**4. 新 DEX 接入慢**

新 DEX 上线 → 你的 indexer 还没解析逻辑 → 用户看不到数据。对策：插件化解析器 + 快速开发流程。

**5. 攻击者污染数据**

恶意 Token 构造特殊事件试图让 indexer 崩溃。对策：所有解析器加 try/catch + 沙盒执行。

### 对中小平台的启示

不是所有平台都要从零造 indexer。合理的策略：

**初期（< 1M 用户）**：用 Helius / Shyft / Bitquery 等**托管 indexer**

- 成本低（$1K-$10K/月）
- 上线快
- 数据相对全

**成长期（1M-10M 用户）**：**混合模式**

- 托管服务做基础数据
- 自建 indexer 处理平台独有逻辑（如 Smart Money 算法）

**头部（10M+ 用户）**：**全自建**

- 成本合理（规模效应）
- 数据完全可控
- 差异化的核心（如 GMGN 的 Smart Money、Axiom 的速度）

**错误选择**：
- 早期就自建 → 烧钱烧到破产
- 永远用托管 → 没有差异化，被头部吊打

### 工程经验

**1. 日志和监控比代码更重要**

indexer 是"永远在跑"的系统，任何问题都要立刻发现。核心指标：
- 延迟（从链上事件到 DB）
- 丢失率（应该索引多少 vs 实际索引多少）
- 重复率（reorg 后的去重）

**2. 设计要为 reorg 做准备**

即使 Solana 几乎不 reorg，也要当它会发生来设计。不然某天一次大 reorg 会让你的数据库彻底错乱。

**3. 不要相信链上数据的"完整性"**

- 你订阅的 RPC 可能漏事件
- 新 DEX 可能有你没见过的指令结构
- 恶意 Token 可能构造畸形数据

**永远假设输入是不可信的**，严格做输入验证。

**4. 读写分离是刚需**

写入（新事件）和读取（用户查询）的模式完全不同。不分离的话，一个热点 Token 的查询能把整个数据库拖死。

## 经验教训

1. **Indexer 是 Meme 平台的真正护城河** —— 不是 UI，不是路由，是把链上数据变成可用信号的能力
2. **数据分层是必须的** —— 一个数据库不可能同时扛住热/温/冷所有查询模式
3. **Reorg 是最容易写错的部分** —— 必须从设计开始考虑，不能事后补救
4. **Yellowstone gRPC 已经是 Solana 事实标准** —— 想自建必须用，甚至升级到 ShredStream
5. **TiDB 是头部平台的流行选择** —— HTAP 特性完美匹配 Meme 数据的访问模式
6. **Indexer 的月成本是 $200K+** —— 这是为什么中小平台活不下去
7. **初期用托管，成长期混合，头部自建** —— 不要在错误的阶段做错误的选择

---

*相关：[平台架构总览 ←](./4-1-architecture-overview.md) · [Token 发现引擎 →](./4-2-token-discovery-engine.md) · [头部 Meme 平台基建 →](./8-3f-meme-platform-infrastructure.md)*
