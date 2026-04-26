# Meme Trade Wiki 🐸

**一个 Meme 交易平台工程师写的行业内幕手册。**

---

## 这是什么

2024 年，Pump.fun 在 Solana 上点燃了 Meme 币狂潮。短短一年多，围绕 Meme 币交易诞生了一个完整的生态 —— 发射台、交易平台、狙击机器人、跟单工具、数据分析、MEV 套利 —— 累计创造了数十亿美元的交易量和超过十亿美元的收入。

我在过去两年里参与建设了其中一个交易平台。这个知识库是我对这个行业的全面拆解 —— 从市场格局到技术内幕，从玩家生态到黑暗面。

**这不是入门教程，这是一份来自内部的行业田野调查。**

---

## 适合谁看

| 你是谁 | 从哪开始 | 你会得到什么 |
|--------|----------|-------------|
| 🟢 **好奇的普通人** | 第一章 | 理解 Meme 币世界到底在玩什么 |
| 🟡 **Meme 币交易者** | 第二、三章 | 知道平台背后的规则和潜规则 |
| 🔵 **想入行的开发者** | 第四、五章 | 了解平台的技术架构和实现 |
| 🔴 **资深从业者** | 第六、七章 | 深挖链上机制和工程难题 |

---

## 📖 目录

### 第一章：Meme 币世界入门

> 从零开始理解这个疯狂的市场。不需要任何技术背景。

| # | 标题 | 关键词 | 状态 |
|---|------|--------|------|
| 1.1 | [什么是 Meme 币：从 Doge 到 Pump.fun 的进化史](./articles/1-1-what-is-memecoin.md) | 起源、文化、为什么有人买 | � |
| 1.2 | [一个 Meme 币的一生：从诞生到归零的 5 分钟](./articles/1-2-lifecycle-of-a-memecoin.md) | 发射、bonding curve、毕业、暴涨、归零 | 🔜 |
| 1.3 | [Meme 币市场的参与者图谱](./articles/1-3-player-map.md) | 发射台、交易平台、做市商、狙击手、散户、KOL | 🔜 |
| 1.4 | [钱从哪来，到哪去：Meme 币的资金流动全景](./articles/1-4-money-flow.md) | 手续费、MEV、Rug Pull、谁在赚谁的钱 | 🔜 |
| 1.5 | [99% 的 Meme 币归零：幸存者偏差与真实胜率](./articles/1-5-survival-bias.md) | 数据真相、概率、风险认知 | 🔜 |

### 第二章：市场格局与商业内幕

> 这盘生意是怎么运转的。平台方视角的行业分析。

| # | 标题 | 关键词 | 状态 |
|---|------|--------|------|
| 2.1 | [Pump.fun 如何凭空创造了一个十亿美元市场](./articles/2-1-how-pumpfun-created-a-market.md) | bonding curve 创新、市场引爆、生态效应 | 🔜 |
| 2.2 | [发射台之战：Pump.fun vs LetsBonk vs Moonshot vs 其他](./articles/2-2-launchpad-wars.md) | 发射台对比、机制差异、市场份额 | 🔜 |
| 2.3 | [交易平台之战：Axiom vs GMGN vs Photon vs BullX](./articles/2-3-platform-wars.md) | 平台对比、功能差异、谁赢了谁死了 | 🔜 |
| 2.4 | [平台怎么赚钱：手续费、MEV、推荐返佣的商业模式拆解](./articles/2-4-business-models.md) | 收入结构、定价策略、盈利能力 | 🔜 |
| 2.5 | [从狂热到寒冬：Meme 交易市场的周期与生存法则](./articles/2-5-market-cycles.md) | 牛熊转换、平台转型、行业整合 | 🔜 |
| 2.6 | [Telegram Bot vs Web 平台：两条路线的竞争与融合](./articles/2-6-tg-bot-vs-web.md) | 产品形态、用户习惯、技术差异 | 🔜 |

### 第三章：交易者的生存指南

> 作为平台方，我看到的交易者行为模式和真实生存法则。

| # | 标题 | 关键词 | 状态 |
|---|------|--------|------|
| 3.1 | [Meme 币交易的核心概念：滑点、Gas、Priority Fee、Jito Tip](./articles/3-1-core-concepts.md) | 基础概念、为什么你的交易比别人贵 | 🔜 |
| 3.2 | [怎么发现新币：Token Radar 的工作原理](./articles/3-2-token-discovery.md) | 新币发现、过滤、信号判断 | 🔜 |
| 3.3 | [Smart Money 追踪：跟聪明钱到底有没有用](./articles/3-3-smart-money.md) | 钱包追踪、跟单逻辑、真实胜率 | 🔜 |
| 3.4 | [狙击（Sniping）：抢跑的艺术与代价](./articles/3-4-sniping.md) | 狙击原理、速度竞争、风险 | 🔜 |
| 3.5 | [如何识别 Rug Pull：从合约到链上行为的红旗信号](./articles/3-5-rug-detection.md) | 蜜罐、Dev 钱包、LP 锁定、权限检查 | 🔜 |
| 3.6 | [你的交易为什么被夹了：三明治攻击完全解读](./articles/3-6-sandwich-attack.md) | MEV、frontrun、backrun、防护措施 | 🔜 |
| 3.7 | [止盈止损和自动交易：链上自动化的真实体验](./articles/3-7-auto-trading.md) | TP/SL、DCA、限价单、链上执行 | 🔜 |

### 第四章：平台架构全景

> 一个 Meme 交易平台到底是怎么造出来的。面向开发者。

| # | 标题 | 关键词 | 状态 |
|---|------|--------|------|
| 4.1 | [系统架构总览：一个 Meme 交易平台的组成](./articles/4-1-architecture-overview.md) | 组件、数据流、技术选型 | 🔜 |
| 4.2 | [Token 发现引擎：如何实时捕获新币](./articles/4-2-token-discovery-engine.md) | 链上事件监听、索引、过滤策略 | 🔜 |
| 4.3 | [行情数据管线：从原始区块到实时 K 线](./articles/4-3-market-data-pipeline.md) | 价格计算、OHLCV、WebSocket 推送 | 🔜 |
| 4.4 | [交易引擎：一笔买单如何变成链上交易](./articles/4-4-trading-engine.md) | 订单构建、路由选择、交易执行 | 🔜 |
| 4.5 | [钱包系统设计：托管、非托管与混合方案](./articles/4-5-wallet-system.md) | 密钥管理、签名流程、安全架构 | 🔜 |
| 4.6 | [跟单系统：从钱包监控到自动执行的全链路](./articles/4-6-copy-trading-system.md) | 钱包追踪、交易解析、执行策略 | 🔜 |
| 4.7 | [Token 安全评分系统：自动化风险检测](./articles/4-7-token-security-scoring.md) | 合约分析、持仓分布、风险模型 | 🔜 |

### 第五章：Solana 链上机制深挖

> Meme 交易背后的 Solana 特有机制。理解这些才能理解平台为什么这么设计。

| # | 标题 | 关键词 | 状态 |
|---|------|--------|------|
| 5.1 | [Solana 交易机制：Compute Unit、Priority Fee 与 Versioned Transaction](./articles/5-1-solana-tx-mechanics.md) | 交易构建、费用模型、签名机制 | 🔜 |
| 5.2 | [DEX 全景：Raydium、Jupiter、Pump.fun、PumpSwap、Meteora](./articles/5-2-dex-landscape.md) | AMM 机制、路由、流动性、集成方式 | 🔜 |
| 5.3 | [Jito 与 MEV：每笔交易背后的隐形税](./articles/5-3-jito-and-mev.md) | Bundle、Tip、Block Engine、BAM | 🔜 |
| 5.4 | [RPC 的学问：节点选择如何决定你的交易命运](./articles/5-4-rpc-strategy.md) | 延迟、可靠性、Staked Connection、供应商对比 | 🔜 |
| 5.5 | [交易为什么失败：Landing Rate 的真相与优化](./articles/5-5-tx-landing-rate.md) | 模拟、重试、拥堵、失败原因分析 | 🔜 |
| 5.6 | [Bonding Curve 数学原理与实现](./articles/5-6-bonding-curve.md) | 定价公式、毕业机制、流动性迁移 | 🔜 |

### 第六章：工程难题

> 让平台工程师睡不着觉的问题。面向资深开发者。

| # | 标题 | 关键词 | 状态 |
|---|------|--------|------|
| 6.1 | [延迟之战：毫秒级优化的工程实践](./articles/6-1-latency-war.md) | 网络优化、co-location、执行路径 | 🔜 |
| 6.2 | [当 Solana 宕机：网络降级下的平台生存策略](./articles/6-2-solana-degradation.md) | 拥堵、TPS 下降、降级方案、恢复 | 🔜 |
| 6.3 | [平台安全：你没想到的攻击面](./articles/6-3-platform-security.md) | 资金安全、API 攻击、钓鱼、运维安全 | 🔜 |
| 6.4 | [从 100 到 10 万用户：实时交易平台的扩容之路](./articles/6-4-scaling.md) | 架构演进、瓶颈、数据库、缓存 | 🔜 |
| 6.5 | [高可用与容灾：交易平台不能挂](./articles/6-5-high-availability.md) | 多活、故障转移、监控告警 | 🔜 |

### 第七章：黑暗面

> 这个行业不愿意公开谈论的事情。

| # | 标题 | 关键词 | 状态 |
|---|------|--------|------|
| 7.1 | [Cabal（小圈子）：Meme 币市场的幕后操盘手](./articles/7-1-cabal.md) | 内幕交易、协调拉盘、KOL 合谋 | 🔜 |
| 7.2 | [刷量与假数据：你看到的交易量有多少是真的](./articles/7-2-wash-trading.md) | 刷量手法、识别方法、平台态度 | 🔜 |
| 7.3 | [Rug Pull 产业链：从发币到跑路的完整流程](./articles/7-3-rug-pull-industry.md) | 技术手段、社会工程、产业化运作 | 🔜 |
| 7.4 | [平台的灰色地带：我们不说但你应该知道的事](./articles/7-4-platform-grey-area.md) | 利益冲突、数据优势、道德边界 | 🔜 |
| 7.5 | [监管来了吗：Meme 币交易的法律风险](./articles/7-5-regulation.md) | 各国监管态度、合规风险、未来趋势 | 🔜 |

---

## 附录

| 标题 | 说明 |
|------|------|
| [术语表](./appendix/glossary.md) | Meme 交易世界的专业术语解释 |
| [工具与资源](./appendix/tools-and-resources.md) | 推荐的工具、数据源、学习资料 |
| [数据仪表盘](./appendix/data-dashboard.md) | 关键市场数据和链接 |
| [常见问题 FAQ](./appendix/faq.md) | 读者最常问的问题 |

---

## 阅读指南

每篇文章遵循统一结构：

1. **表面认知** —— 大多数人以为的样子
2. **真实情况** —— 平台方/内部人看到的真相
3. **技术细节** —— 代码、架构、数据（视文章深度而定）
4. **经验教训** —— 我的个人复盘和建议

---

## 关于作者

交易所工程师，在 Solana 上建设 Meme 币交易基础设施两年。现在把学到的东西分享出来。

这是一个持续更新的项目。

[![Twitter](https://img.shields.io/badge/𝕏_%40FrankFu2262-000?style=flat-square&logo=x&logoColor=white)](https://x.com/FrankFu2262)

## 许可证

[CC BY-SA 4.0](./LICENSE) —— 自由分享和改编，需注明出处。
