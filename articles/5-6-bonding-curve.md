# Bonding Curve 数学原理与实现

> Bonding Curve 是 Pump.fun 的核心创新。理解它的数学原理，就理解了 Meme 币定价的底层逻辑。

## 表面认知

"买的人多价格就涨，卖的人多价格就跌。" 原理没错，但具体怎么涨、涨多少，是由数学公式决定的。

## 真实情况

### 什么是 Bonding Curve

Bonding Curve 是一个数学函数，定义了 Token 价格和供应量之间的关系：

```
价格 = f(供应量)
```

当有人买入时，供应量增加，价格上升。
当有人卖出时，供应量减少，价格下降。

### Pump.fun 的 Bonding Curve 模型

Pump.fun 使用的是一种**恒定乘积**变体：

核心概念：
- 有一个虚拟的 SOL 储备池和 Token 储备池
- 两者的乘积保持恒定：`SOL_reserve × Token_reserve = k`
- 买入 Token = 向池中注入 SOL，取出 Token
- 卖出 Token = 向池中注入 Token，取出 SOL

**价格计算：**
```
当前价格 = SOL_reserve / Token_reserve
```

**买入 N 个 Token 需要的 SOL：**
```
cost = SOL_reserve × (1 - Token_reserve / (Token_reserve + N))
```

**卖出 N 个 Token 获得的 SOL：**
```
proceeds = SOL_reserve × (1 - (Token_reserve - N) / Token_reserve)
```

### 毕业机制

当 Bonding Curve 中的 SOL 储备达到约 85 SOL（对应约 $69K 市值）时：

1. Pump.fun 从 Bonding Curve 中提取所有 SOL 储备
2. 铸造对应数量的 Token
3. 在 PumpSwap 上创建 SOL/Token 流动性池
4. 注入 SOL 和 Token 作为初始流动性
5. 销毁 LP Token

这个过程是自动的，不需要人工干预。

### Bonding Curve 的特性

**1. 早期买入优势巨大**
- 曲线的斜率在早期很平缓
- 最早的买家用很少的 SOL 就能买到大量 Token
- 这就是为什么狙击手要抢在第一时间买入

**2. 价格影响随金额增大**
- 买入金额越大，对价格的推动越大
- 在流动性很低的早期，0.1 SOL 的买入可能让价格翻倍

**3. 卖出也会显著影响价格**
- 大额卖出会让价格大幅下跌
- 这就是为什么"Dev 卖出"会导致价格崩盘

**4. 无常损失不存在**
- 因为没有传统的 LP 提供者
- 所有流动性由 Bonding Curve 公式自动管理

### 对交易平台的影响

平台在集成 Bonding Curve 交易时需要注意：

1. **价格计算**：需要从链上读取 Bonding Curve 的状态来计算实时价格
2. **滑点预估**：根据买入金额和当前储备计算预期滑点
3. **毕业检测**：监控 Bonding Curve 状态，在毕业时及时切换到 DEX 交易
4. **价格连续性**：毕业前后价格可能有跳变，需要平滑处理

## 经验教训

1. **Bonding Curve 的数学决定了 Meme 币的经济学** —— 早期买入者天然有优势
2. **理解公式才能理解价格行为** —— 为什么小额买入就能让价格暴涨
3. **毕业是关键转折点** —— 从 Bonding Curve 到 DEX 的切换改变了交易动态
4. **Bonding Curve 不是万能的** —— 它解决了流动性冷启动，但不能防止操纵

---

*下一篇：[延迟之战：毫秒级优化的工程实践 →](./6-1-latency-war.md)*
