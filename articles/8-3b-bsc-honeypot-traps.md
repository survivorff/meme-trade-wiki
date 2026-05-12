# BSC Meme 的 Honeypot 与合约陷阱：为什么你的交易"成功了"但卖不出去

> BSC 上的 Honeypot 比 Solana 更猖獗、更隐蔽、更难防。这不是因为 BSC 更"坏"，而是 EVM 的合约灵活性给了骗子更多操作空间。

## 表面认知

"Honeypot 就是买得进卖不出的币，我用检测工具扫一下就行了。" 2024 年之前也许是这样。2025-2026 年的 BSC Honeypot 已经进化到**检测工具也会被骗**的程度。

## 真实情况

### 什么是 Honeypot（精确定义）

Honeypot Token 是一种恶意合约，设计目标是：

1. **买入正常**：用户能在 PancakeSwap 或聚合器上正常买入
2. **卖出受限**：通过合约逻辑阻止、限制或经济性惩罚卖出操作
3. **表面合法**：BscScan 上合约已验证、价格图表正常、有交易量

关键区分：
- **硬 Honeypot**：卖出直接 revert，你的 Token 永远卖不掉
- **软 Honeypot**：卖出"成功"但被收 70-99% 的税，你拿回来的钱几乎为零
- **延时 Honeypot**：上线初期正常，过一段时间（或达到某个条件）后变成 Honeypot

### BSC 上 Honeypot 的 7 种技术手段

#### 1. 黑名单（Blacklist）— 最基础

```solidity
mapping(address => bool) private _blacklist;

function _transfer(address from, address to, uint256 amount) internal {
    require(!_blacklist[from], "Blacklisted");
    // ... normal transfer
}
```

**机制**：买入后你的地址被加入黑名单，卖出时 `require` 失败，交易 revert。

**识别难度**：低。检测工具模拟卖出就能发现。

**进化版**：黑名单不在合约里硬编码，而是通过**外部合约调用**（`IBlacklist(externalAddr).isBlocked(from)`）。这样主合约看起来干净，黑名单逻辑藏在另一个合约里。

#### 2. 动态卖出税（Hidden Sell Tax）— 最常见

```solidity
uint256 private _sellTax = 5; // 看起来 5%

function setSellTax(uint256 newTax) external onlyOwner {
    _sellTax = newTax; // 上线后改成 99%
}
```

**机制**：
- 上线时卖出税 5%（正常）
- 吸引买入后，Owner 调用 `setSellTax(99)`
- 你卖出 $100 的 Token，只拿回 $1

**识别难度**：中。检测工具在**当前时刻**模拟卖出是正常的（税还是 5%），但你买入后税就变了。

**进化版**：
- 税率不是固定值，而是根据**持仓时间**动态计算（持有 > 1 小时后税率飙升）
- 税率根据**卖出金额**变化（小额卖出正常，大额卖出 99% 税）
- 税率根据**总卖出次数**变化（前 10 笔卖出正常，第 11 笔开始 99%）

#### 3. 最大交易限制（Max Transaction / Max Wallet）

```solidity
uint256 public maxTxAmount = totalSupply * 1 / 100; // 最多卖 1%

function _transfer(...) internal {
    if (to == pancakeswapPair) { // 卖出
        require(amount <= maxTxAmount, "Exceeds max tx");
    }
}
```

**机制**：
- 你能买任意数量
- 但卖出时限制每笔最多卖总量的 0.1%-1%
- 配合高 Gas 成本，你需要几十笔交易才能卖完，实际上不可能

**识别难度**：中低。但很多"合法"Token 也有 maxTx 限制（防鲸鱼），所以检测工具不一定报警。

**进化版**：maxTxAmount 初始设为合理值（5%），上线后 Owner 改成 0.001%。

#### 4. 暂停交易（Pausable）

```solidity
bool public tradingEnabled = true;

function _transfer(...) internal {
    require(tradingEnabled || from == owner(), "Trading paused");
}

function pauseTrading() external onlyOwner {
    tradingEnabled = false;
}
```

**机制**：Owner 随时可以暂停所有交易。你的 Token 被锁死在钱包里。

**识别难度**：低（如果合约开源）。但很多合约用 `internal` 函数或 proxy 模式隐藏这个功能。

#### 5. 外部合约依赖（External Call Honeypot）— 最隐蔽

```solidity
address private _router;

function _transfer(address from, address to, uint256 amount) internal {
    // 看起来是正常的路由检查
    require(IRouter(_router).canTransfer(from, to, amount), "Router check failed");
    // ...
}
```

**机制**：
- 主合约代码看起来完全正常
- 但 `_transfer` 里调用了一个**外部合约**的函数
- 外部合约的逻辑可以随时被 Owner 修改（通过 proxy 或直接部署新合约）
- 上线时 `canTransfer` 返回 true，吸引买入后改成返回 false

**识别难度**：**高**。检测工具模拟当前状态是正常的。只有读懂合约代码 + 追踪外部合约才能发现。

**这是 2025-2026 年 BSC 上最流行的 Honeypot 手法**。

#### 6. Approve 陷阱

```solidity
function approve(address spender, uint256 amount) public returns (bool) {
    if (spender == pancakeswapRouter) {
        // 对 DEX 的 approve 正常
        _approve(msg.sender, spender, amount);
    } else {
        // 对其他地址的 approve 静默失败（不 revert，但不生效）
        emit Approval(msg.sender, spender, 0);
    }
    return true;
}
```

**机制**：
- 你 approve PancakeSwap Router 卖出 → 看起来成功了
- 但实际 allowance 没有真正设置
- 卖出时因为 allowance 不足而失败

**识别难度**：中。需要检查 approve 后的实际 allowance 值。

#### 7. LP 操控（Liquidity Rug + Honeypot 组合）

不是传统意义的 Honeypot，但效果类似：

**机制**：
- Dev 创建 Token + 添加流动性
- 价格被人为拉高（Dev 自买自卖）
- 散户 FOMO 买入
- Dev 移除流动性（LP 被抽走）
- 你的 Token 还在，但**没有流动性可以卖出**

**和纯 Honeypot 的区别**：合约本身没有限制卖出，但市场上没有买家。

**BSC 特有问题**：BSC 上很多 Token 的 LP 没有锁定（Solana 上 Pump.fun 毕业的 Token LP 自动锁定）。

### 为什么 BSC 上 Honeypot 比 Solana 多

| 因素 | BSC | Solana |
|------|-----|--------|
| 合约灵活性 | EVM Solidity，极其灵活 | Rust + Anchor，限制更多 |
| 合约可升级 | Proxy 模式普遍，Owner 可改逻辑 | Program 升级需要 authority，但 Pump.fun Token 不可升级 |
| 发射台标准化 | 无统一标准，每个 Token 合约不同 | Pump.fun 统一合约，所有 Token 逻辑相同 |
| 合约验证 | BscScan 验证 ≠ 安全 | Solana 上 Token 大多用标准 SPL Token 程序 |
| 检测难度 | 高（外部调用、proxy、动态税） | 低（标准 Token 程序，异常行为容易识别） |

**核心差异**：Solana 上 Pump.fun 发射的 Token 用的是**统一的 Token 程序**，没有自定义 transfer 逻辑的空间。BSC 上每个 Token 都是独立的 Solidity 合约，**可以写任何逻辑**。

### 检测工具与局限

**常用检测工具：**

| 工具 | 原理 | 局限 |
|------|------|------|
| [Honeypot.is](https://honeypot.is) | 模拟买入+卖出，检查是否成功 | 只检测当前状态，延时 Honeypot 检测不到 |
| [Token Sniffer](https://tokensniffer.com) | 合约代码分析 + 模拟交易 | 外部合约依赖型检测不到 |
| [GoPlus Security](https://gopluslabs.io) | 多维度安全评分 | 新合约可能还没被收录 |
| [De.Fi Scanner](https://de.fi/scanner) | 合约审计 + 持仓分析 | 免费版功能有限 |
| [GMGN 内置检测](https://gmgn.ai) | 买卖模拟 + 税率检测 | 和 Honeypot.is 类似的局限 |

**检测工具的根本局限：**

1. **只能检测当前状态**：如果 Honeypot 是"延时触发"的，检测时一切正常
2. **外部合约不透明**：工具通常只分析主合约，不追踪所有外部调用
3. **Proxy 合约**：逻辑可以被 Owner 随时替换，检测时的逻辑 ≠ 你买入后的逻辑
4. **经济型 Honeypot**：税率从 5% 变成 99% 不会让交易 revert，工具可能不报警

### 实战防御清单

**买入前（5 分钟检查）：**

1. ✅ 用 Honeypot.is 或 GoPlus 扫一遍（过滤掉 80% 的低级 Honeypot）
2. ✅ 在 BscScan 上看合约是否 verified（未验证 = 极高风险）
3. ✅ 检查 Owner 权限：有没有 `setSellTax`、`pause`、`blacklist` 函数
4. ✅ 检查 LP 是否锁定（用 Team.Finance 或 Unicrypt 查）
5. ✅ 看 Token 的持仓分布：前 10 地址持有 > 50% = 高风险
6. ✅ 看交易历史：只有买入没有卖出 = 几乎确定是 Honeypot

**买入后（立刻验证）：**

7. ✅ **小额测试卖出**：先买 $5-10，立刻尝试卖出。能卖 = 暂时安全。不能卖 = 立刻止损（虽然已经亏了这 $5-10）
8. ✅ 检查实际到账金额：卖出 $10 的 Token，实际收到多少 BNB？如果只收到 $1 = 隐藏高税

**持续监控：**

9. ✅ 设置合约事件监控（如果你会编程）：监听 Owner 调用 `setSellTax` 等函数
10. ✅ 关注社群动态：如果 Telegram 群突然禁言 / 管理员消失 = 跑路信号

### BSC Honeypot 的规模

根据多个安全平台的数据（2025-2026）：

- BSC 上每天新创建的 Token 中，**估计 30-50% 是某种形式的 Honeypot 或 Rug Pull**
- 平均存活时间：2-48 小时（吸引足够买入后跑路）
- 单个 Honeypot 的平均获利：$500-$5000（小额但量大）
- 产业化运作：同一个团队可能同时运营 10-50 个 Honeypot Token

### 和 Solana 的 Rug Pull 对比

| 维度 | BSC Honeypot | Solana Rug Pull |
|------|-------------|-----------------|
| 主要手段 | 合约限制卖出 | Dev 抛售 / 撤 LP |
| 技术门槛 | 中（需要写 Solidity） | 低（Pump.fun 上直接操作） |
| 检测难度 | 高（合约可升级、外部依赖） | 中（标准合约，看 Dev 钱包行为） |
| 用户损失方式 | Token 卖不掉 | Token 价格归零 |
| 恢复可能 | 几乎为零 | 几乎为零 |
| 法律追溯 | 极难（匿名合约部署） | 极难（同） |

## 经验教训

1. **BscScan 上"已验证"不等于安全** —— 验证只是说代码公开了，不代表代码没有恶意逻辑
2. **检测工具只能过滤低级 Honeypot** —— 高级的延时型、外部依赖型检测不到
3. **小额测试卖出是最可靠的验证** —— 花 $5 买个安心，比亏 $500 强
4. **BSC 上没有"标准 Token 程序"** —— 每个 Token 都是独立合约，每个都可能有陷阱
5. **LP 未锁定 = 随时可能被抽** —— 这是 BSC 上最常见的"软 Rug"
6. **如果一个 Token 只有买入没有卖出记录，100% 是 Honeypot** —— 不要心存侥幸

---

*下一篇：[BSC Meme 工具与钱包 →](./8-3c-bsc-tools-wallets.md)*
