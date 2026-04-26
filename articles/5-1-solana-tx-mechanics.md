# Solana 交易机制：Compute Unit、Priority Fee 与 Versioned Transaction

> 理解 Solana 交易的底层机制，是理解 Meme 交易平台所有技术决策的基础。

## 表面认知

"Solana 交易就是发一笔 Transaction 到链上。" 没错，但这笔 Transaction 的构建方式直接决定了它能不能成功、多快成功、花多少钱。

## 真实情况

### Solana 交易的基本结构

一笔 Solana 交易包含：
- **签名**：证明交易是由私钥持有者发起的
- **消息**：交易的实际内容
  - **Header**：签名者数量、只读账户数量
  - **Account Keys**：交易涉及的所有账户地址
  - **Recent Blockhash**：最近的区块哈希（防重放 + 过期机制）
  - **Instructions**：要执行的指令列表

### Compute Unit（CU）

**是什么：** Solana 用 CU 来衡量交易的计算复杂度，类似以太坊的 Gas。

**关键参数：**
- 每笔交易默认上限：200,000 CU
- 可以通过 `SetComputeUnitLimit` 指令调整（最高 1,400,000 CU）
- 简单转账：~150 CU
- DEX Swap：50,000-300,000 CU
- 复杂的多跳路由：可能超过 500,000 CU

**Compute Unit Price：**
- 通过 `SetComputeUnitPrice` 指令设置
- 单位：micro-lamports per CU
- 实际费用 = CU Limit × CU Price
- 这就是 Priority Fee 的计算方式

**最佳实践：**
```
1. 先模拟交易，获取实际 CU 消耗
2. CU Limit = 实际消耗 × 1.2（留 20% 余量）
3. CU Price 根据网络拥堵程度动态调整
```

设置不当的后果：
- CU Limit 太低 → 交易执行到一半失败，但费用已扣
- CU Limit 太高 → 不会多扣费，但可能影响调度优先级
- CU Price 太低 → 交易可能不被打包
- CU Price 太高 → 浪费钱

### Priority Fee

Solana 的交易调度器会优先处理 Priority Fee 更高的交易。在 Meme 交易场景中：

**正常时期：**
- 大多数交易不需要很高的 Priority Fee
- 0.00001-0.0001 SOL 就够了

**热门 Token 发射时：**
- 大量交易竞争有限的区块空间
- Priority Fee 可能飙升到 0.01-0.1 SOL
- 不设足够的 Priority Fee，交易可能被丢弃

**动态调整策略：**
- 查询最近区块的 Priority Fee 分布
- 根据目标确认速度选择合适的百分位
- 例如：要求快速确认 → 使用 P75-P90 的 Priority Fee

### Versioned Transaction

Solana 有两种交易格式：

**Legacy Transaction：**
- 旧格式，最多支持 35 个账户
- 所有账户地址直接列在交易中

**Versioned Transaction（V0）：**
- 新格式，支持 Address Lookup Table（ALT）
- 可以引用预先创建的地址表，减少交易大小
- 支持更多账户（对复杂的 DEX 交易很重要）

**为什么重要：**
- 复杂的 DEX Swap（多跳路由）可能涉及几十个账户
- Legacy Transaction 可能放不下
- V0 Transaction + ALT 可以解决这个问题

### Recent Blockhash 与交易过期

- 每笔交易必须包含一个 Recent Blockhash
- 这个 Blockhash 在约 150 个区块（~1 分钟）后过期
- 过期的交易不会被执行

**影响：**
- 交易构建后需要尽快提交
- 如果交易长时间未确认，需要用新的 Blockhash 重新构建
- 这是交易重试逻辑的基础

### 交易确认级别

Solana 有多个确认级别：

| 级别 | 含义 | 延迟 |
|------|------|------|
| processed | 被节点处理 | ~400ms |
| confirmed | 被超级多数验证者确认 | ~400ms-1s |
| finalized | 不可逆转 | ~6-12s |

对于 Meme 交易：
- 大多数操作使用 `confirmed` 级别就够了
- 涉及大额资金的操作应该等待 `finalized`

## 经验教训

1. **CU 管理是性能优化的关键** —— 精确设置 CU 可以降低成本和提高成功率
2. **Priority Fee 需要动态调整** —— 固定值在网络波动时会出问题
3. **使用 V0 Transaction** —— 对复杂交易来说几乎是必须的
4. **理解交易过期机制** —— 这影响了重试策略的设计

---

*下一篇：[DEX 全景 →](./5-2-dex-landscape.md)*
