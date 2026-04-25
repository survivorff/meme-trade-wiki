# Meme Trade Wiki 🐸

**The anatomy of a Solana meme trading platform — from the inside.**

A comprehensive technical knowledge base covering the business logic, system architecture, on-chain mechanics, and market evolution of meme coin trading platforms (like Axiom, GMGN, Photon, BullX, etc.) on Solana.

Written by an engineer who spent 2 years building one.

---

## Why This Exists

Since Pump.fun ignited the meme coin frenzy in early 2024, an entire ecosystem of trading platforms has emerged — generating billions in volume and hundreds of millions in revenue. Yet there's almost zero public documentation on how these platforms actually work under the hood.

This repo fills that gap. Every article is written from the platform builder's perspective, with real engineering trade-offs, not surface-level overviews.

---

## 📖 Table of Contents

### Part 1: Market & Business

The meme trading landscape — what happened, who the players are, and how the money flows.

| # | Article | Status |
|---|---------|--------|
| 1.1 | [The Rise of Meme Trading Platforms: A Timeline (2024-2026)](./articles/1-1-market-timeline.md) | 📝 Draft |
| 1.2 | [Business Models: How Platforms Make Money](./articles/1-2-business-models.md) | 🔜 |
| 1.3 | [Competitive Landscape: Axiom vs GMGN vs Photon vs BullX](./articles/1-3-competitive-landscape.md) | 🔜 |
| 1.4 | [User Segmentation: Who Trades Meme Coins and How](./articles/1-4-user-segmentation.md) | 🔜 |
| 1.5 | [Bear Market Impact: How Platforms Survive the Downturn](./articles/1-5-bear-market.md) | 🔜 |

### Part 2: Core Architecture

How a meme trading platform is built from scratch.

| # | Article | Status |
|---|---------|--------|
| 2.1 | [System Architecture Overview: What Makes a Meme Trading Platform](./articles/2-1-architecture-overview.md) | 🔜 |
| 2.2 | [Token Discovery Engine: How to Find New Tokens in Real-Time](./articles/2-2-token-discovery.md) | 🔜 |
| 2.3 | [Price & Market Data Pipeline: From On-Chain to User Screen](./articles/2-3-market-data-pipeline.md) | 🔜 |
| 2.4 | [Trading Engine: Order Construction, Routing & Execution](./articles/2-4-trading-engine.md) | 🔜 |
| 2.5 | [Wallet & Account System Design](./articles/2-5-wallet-system.md) | 🔜 |

### Part 3: On-Chain Deep Dive

The Solana-specific mechanics that make meme trading possible.

| # | Article | Status |
|---|---------|--------|
| 3.1 | [Solana Transaction Lifecycle for Trading Platforms](./articles/3-1-solana-tx-lifecycle.md) | 🔜 |
| 3.2 | [DEX Integration: Raydium, Jupiter, Pump.fun & Meteora](./articles/3-2-dex-integration.md) | 🔜 |
| 3.3 | [MEV on Solana: Jito Bundles, Tips & Sandwich Protection](./articles/3-3-mev-and-jito.md) | 🔜 |
| 3.4 | [RPC Strategy: Node Selection, Failover & Performance](./articles/3-4-rpc-strategy.md) | 🔜 |
| 3.5 | [Transaction Landing Rate: Why Trades Fail and How to Fix It](./articles/3-5-tx-landing-rate.md) | 🔜 |

### Part 4: Platform Features

The features that differentiate platforms and keep users coming back.

| # | Article | Status |
|---|---------|--------|
| 4.1 | [Sniping: The Art of Being First](./articles/4-1-sniping.md) | 🔜 |
| 4.2 | [Copy Trading: Smart Money Tracking & Execution](./articles/4-2-copy-trading.md) | 🔜 |
| 4.3 | [Auto-Buy / Auto-Sell / Take-Profit / Stop-Loss](./articles/4-3-automation.md) | 🔜 |
| 4.4 | [Token Security Scoring: Rug Pull Detection](./articles/4-4-token-security.md) | 🔜 |
| 4.5 | [Analytics Dashboard: On-Chain Data That Matters](./articles/4-5-analytics.md) | 🔜 |

### Part 5: Hard Problems

The engineering challenges that keep platform engineers up at night.

| # | Article | Status |
|---|---------|--------|
| 5.1 | [Latency Wars: Milliseconds Matter in Meme Trading](./articles/5-1-latency.md) | 🔜 |
| 5.2 | [Handling Solana Congestion & Degraded Network](./articles/5-2-congestion.md) | 🔜 |
| 5.3 | [Security: Protecting User Funds & Platform Wallets](./articles/5-3-security.md) | 🔜 |
| 5.4 | [Scaling: From 100 to 100K Concurrent Users](./articles/5-4-scaling.md) | 🔜 |
| 5.5 | [Anti-Fraud: Wash Trading, Fake Volume & Bot Detection](./articles/5-5-anti-fraud.md) | 🔜 |

---

## Who Is This For

- **Developers** building trading platforms or DeFi products on Solana
- **Engineers** curious about high-performance on-chain systems
- **Traders** who want to understand what happens behind the "Buy" button
- **Founders** evaluating the meme trading platform space

## About the Author

Exchange engineer. Built meme coin trading infrastructure on Solana for 2 years. Now sharing what I learned.

- 𝕏 [@FrankFu2262](https://x.com/FrankFu2262)

## License

[CC BY-SA 4.0](./LICENSE) — Share and adapt with attribution.
