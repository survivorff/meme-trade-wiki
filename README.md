# Meme Trade Wiki 🐸

**How meme trading platforms really work — told by someone who built one.**

---

## What Is This

In 2024, Pump.fun launched and ignited a meme coin frenzy on Solana. Within months, an entire ecosystem of trading platforms emerged — Axiom, GMGN, Photon, BullX, Trojan — collectively generating billions in volume and over a billion dollars in revenue.

I spent the past 2 years building one of these platforms. This knowledge base is everything I know about how they work — the business, the tech, the tricks, and the hard truths.

**This is not a tutorial. This is an insider's field guide.**

---

## Who Is This For

- 🟢 **Curious traders** — You use these platforms daily but have no idea what happens after you click "Buy". Start with Part 1.
- 🟡 **Aspiring builders** — You want to build a trading tool or platform on Solana. Start with Part 2 and 3.
- 🔴 **Experienced devs & founders** — You want the hard engineering problems and market dynamics. Go straight to Part 4 and 5.

---

## 📖 Table of Contents

### Part 1: The Game — 这盘生意是怎么回事

> For everyone. No code. Understand the market, the money, and the players.

| # | Title | Keywords | Status |
|---|-------|----------|--------|
| 1.1 | [How Pump.fun Created a $1B Market from Nothing](./articles/1-1-how-pumpfun-created-a-market.md) | market origin, bonding curve, token factory | 🔜 |
| 1.2 | [The Platform Wars: Who Won, Who Died, and Why](./articles/1-2-platform-wars.md) | Axiom, GMGN, Photon, BullX, market share | 🔜 |
| 1.3 | [Follow the Money: How Every Player Gets Paid](./articles/1-3-follow-the-money.md) | revenue model, fees, MEV, referral | 🔜 |
| 1.4 | [The Lifecycle of a Meme Coin: 5 Minutes to Glory or Death](./articles/1-4-lifecycle-of-a-memecoin.md) | token launch, pump, dump, survivor | 🔜 |
| 1.5 | [What Happens When the Music Stops: Surviving the Bear](./articles/1-5-surviving-the-bear.md) | downturn, pivot, consolidation | 🔜 |

### Part 2: Under the Hood — 平台到底怎么造的

> For developers. System-level architecture of a meme trading platform.

| # | Title | Keywords | Status |
|---|-------|----------|--------|
| 2.1 | [Architecture Blueprint: What a Meme Trading Platform Looks Like](./articles/2-1-architecture-blueprint.md) | system design, components, data flow | 🔜 |
| 2.2 | [The Token Radar: Discovering New Tokens in Real-Time](./articles/2-2-token-radar.md) | event listening, indexing, filtering | 🔜 |
| 2.3 | [Building the Price Feed: From Raw Blocks to Live Charts](./articles/2-3-price-feed.md) | market data, OHLCV, WebSocket | 🔜 |
| 2.4 | [The Trade Path: How a Buy Order Becomes an On-Chain Transaction](./articles/2-4-trade-path.md) | order flow, tx construction, routing | 🔜 |
| 2.5 | [Wallet Architecture: Custodial, Non-Custodial, and Everything Between](./articles/2-5-wallet-architecture.md) | key management, signing, security | 🔜 |

### Part 3: Solana Internals — 链上那些事

> For blockchain developers. Solana-specific mechanics that make or break your platform.

| # | Title | Keywords | Status |
|---|-------|----------|--------|
| 3.1 | [Solana Transactions for Trading: Everything That Matters](./articles/3-1-solana-tx-for-trading.md) | compute units, priority fees, versioned tx | 🔜 |
| 3.2 | [DEX Landscape: Raydium, Jupiter, Pump.fun, Meteora, PumpSwap](./articles/3-2-dex-landscape.md) | AMM, routing, liquidity, integration | 🔜 |
| 3.3 | [Jito and MEV: The Invisible Tax on Every Trade](./articles/3-3-jito-and-mev.md) | bundles, tips, sandwich, frontrun | 🔜 |
| 3.4 | [The RPC Game: Why Your Node Choice Decides Your Fate](./articles/3-4-rpc-game.md) | latency, reliability, staked connections | 🔜 |
| 3.5 | [Why Transactions Fail: A Field Guide to Landing Rates](./articles/3-5-why-tx-fail.md) | simulation, retry, congestion | 🔜 |

### Part 4: The Features That Win — 功能背后的战争

> For product builders. How each killer feature actually works under the hood.

| # | Title | Keywords | Status |
|---|-------|----------|--------|
| 4.1 | [Sniping: The 200ms Arms Race](./articles/4-1-sniping.md) | speed, Jito bundle, block timing | 🔜 |
| 4.2 | [Copy Trading: Following Smart Money at Machine Speed](./articles/4-2-copy-trading.md) | wallet tracking, execution, slippage | 🔜 |
| 4.3 | [Auto-Trading: Stop-Loss, Take-Profit, and DCA On-Chain](./articles/4-3-auto-trading.md) | automation, monitoring, execution | 🔜 |
| 4.4 | [Rug Detection: Can You Really Spot a Scam Before It Happens?](./articles/4-4-rug-detection.md) | token analysis, honeypot, red flags | 🔜 |
| 4.5 | [The Analytics Edge: What Data Actually Helps Traders Win](./articles/4-5-analytics-edge.md) | on-chain data, smart money, signals | 🔜 |

### Part 5: The Hard Problems — 让人睡不着的难题

> For senior engineers. The challenges that separate toy projects from real platforms.

| # | Title | Keywords | Status |
|---|-------|----------|--------|
| 5.1 | [The Latency Game: When Milliseconds Are Worth Millions](./articles/5-1-latency-game.md) | optimization, co-location, network | 🔜 |
| 5.2 | [When Solana Breaks: Building for Network Degradation](./articles/5-2-when-solana-breaks.md) | congestion, TPS drops, recovery | 🔜 |
| 5.3 | [Securing the Platform: Threats You Haven't Thought Of](./articles/5-3-securing-the-platform.md) | attack vectors, fund safety, ops | 🔜 |
| 5.4 | [From 100 to 100K Users: Scaling a Real-Time Trading Platform](./articles/5-4-scaling.md) | architecture evolution, bottlenecks | 🔜 |
| 5.5 | [The Dark Side: Wash Trading, Manipulation, and What Platforms See](./articles/5-5-dark-side.md) | fraud, detection, ethics | 🔜 |

---

## How to Read This

Each article follows a consistent structure:

- **What you'll learn** — One-line summary
- **The surface** — What most people think they know
- **The reality** — What actually happens (the insider part)
- **Technical deep dive** — Code, architecture, data (where applicable)
- **Lessons learned** — What I'd do differently

---

## About

Exchange engineer. Built meme coin trading infrastructure on Solana. Now sharing what I learned.

This is a living document. New articles are published regularly.

[![Twitter](https://img.shields.io/badge/𝕏_%40FrankFu2262-000?style=flat-square&logo=x&logoColor=white)](https://x.com/FrankFu2262)

## License

[CC BY-SA 4.0](./LICENSE) — Share and adapt with attribution.
