# DEX 全景：Raydium、Jupiter、Pump.fun、PumpSwap、Meteora

> Meme 币的交易最终都发生在 DEX 上。理解每个 DEX 的机制和特点，是平台集成的基础。

## 表面认知

"DEX 就是去中心化交易所，都差不多。" 每个 DEX 的 AMM 模型、费率结构、流动性特征都不同。

## 真实情况

### 主要 DEX 对比

| DEX | 类型 | 手续费 | 特点 | 在 Meme 交易中的角色 |
|-----|------|--------|------|---------------------|
| Raydium | AMM + CLMM | 0.25% | Solana 最老牌的 DEX | Pump.fun 早期毕业 Token 的主要去向 |
| Jupiter | 聚合器 | 0%（平台费）+ 底层 DEX 费 | 聚合多个 DEX 的流动性 | 交易平台最常用的路由引擎 |
| PumpSwap | AMM | 0.25% | Pump.fun 自建 DEX | 现在 Pump.fun 毕业 Token 的主要去向 |
| Meteora | DLMM | 可变 | 动态流动性做市 | 部分 Token 的流动性来源 |
| Orca | CLMM | 可变 | 集中流动性 | 主流 Token 交易 |

### 各 DEX 的 AMM 模型

**Raydium — 恒定乘积 AMM + 集中流动性**
- 经典的 x × y = k 模型
- 也支持集中流动性（CLMM）
- 流动性分布在整个价格范围

**PumpSwap — 恒定乘积 AMM**
- Pump.fun 自建，专门服务毕业后的 Token
- 与 Pump.fun 深度集成，毕业流程无缝衔接
- 创建者可以获得交易量的 0.05% 作为奖励

**Meteora — 动态流动性做市（DLMM）**
- 流动性集中在当前价格附近
- 对做市商更友好（资金效率更高）
- 但对 Meme 币来说，价格波动大，集中流动性可能快速失效

**Jupiter — 聚合器（不是 DEX）**
- 不自己持有流动性
- 聚合 Raydium、PumpSwap、Meteora、Orca 等多个 DEX
- 自动找到最优路由（可能拆分到多个 DEX）
- 对交易平台来说，集成 Jupiter 就等于集成了所有 DEX

### 平台集成策略

**方案一：直接集成各 DEX**
```
优点：延迟最低，控制力最强
缺点：开发量大，需要维护多个 DEX 的适配层
适合：对延迟极度敏感的场景（狙击）
```

**方案二：通过 Jupiter 聚合**
```
优点：一次集成覆盖所有 DEX，自动路由优化
缺点：多一层依赖，延迟略高
适合：大多数普通交易场景
```

**方案三：混合方案**
```
狙击/跟单 → 直接集成目标 DEX（最快）
普通交易 → 通过 Jupiter 聚合（最优价格）
```

大多数成熟平台采用方案三。

### Bonding Curve → DEX 的迁移过程

这是 Meme 币生命周期中的关键转折点：

```
Pump.fun Bonding Curve 阶段
    │
    │ 市值达到 ~$69K
    │
    ▼
毕业触发
    │
    ├── Pump.fun 从 Bonding Curve 中提取 SOL 储备
    ├── 在 PumpSwap 上创建流动性池
    ├── 注入 SOL + Token 作为初始流动性
    └── 销毁 LP Token（防止撤走流动性）
    │
    ▼
Token 在 PumpSwap 上自由交易
    │
    └── Jupiter 等聚合器自动发现并路由
```

### 流动性碎片化问题

同一个 Token 可能在多个 DEX 上有流动性：
- PumpSwap 上有毕业时注入的流动性
- 有人在 Raydium 上也创建了流动性池
- Meteora 上可能也有

这导致：
- 流动性分散，每个池子的深度都不够
- 不同池子的价格可能有差异（套利机会）
- 交易平台需要聚合多个池子才能给用户最优价格

## 经验教训

1. **Jupiter 是大多数场景的最优选择** —— 除非你需要极致的延迟
2. **理解每个 DEX 的费率结构** —— 这直接影响用户的交易成本
3. **流动性碎片化是常态** —— 平台需要有能力聚合多个来源
4. **DEX 格局在快速变化** —— PumpSwap 的出现改变了整个流动性分布

---

*下一篇：[Jito 与 MEV →](./5-3-jito-and-mev.md)*
