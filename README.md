# Panoptic Long Straddle: A Perpetual Options Strategy on Uniswap V3

**Mini-Whitepaper | DeFi Strategy Research**

---

## Abstract

We design, implement, and backtest a **long straddle strategy** using Panoptic's perpetual options protocol built on top of Uniswap V3. The strategy buys synthetic straddles (simultaneous long put + long call at the same strike) when realized volatility is in its lower percentile, targeting profit from subsequent volatility expansion. We implement the full pipeline using the `fractal-defi` framework over the ETH/USDC 0.3% pool on Ethereum mainnet (January 2022 – January 2024) and compare results against a passive Uniswap V3 LP baseline. Grid search over key parameters reveals conditions under which the straddle generates positive risk-adjusted returns.

---

## 1. Problem Statement

### 1.1 Objective

Uniswap V3 liquidity providers are implicitly short options: they collect fees (analogous to option premium) but suffer impermanent loss (analogous to negative gamma). Panoptic formalizes this relationship and enables the inverse position — **buying** an LP position from sellers, thereby going **long volatility**.

The core question this paper addresses:

> *Can a systematic long straddle strategy on Panoptic — entered when pool-implied volatility is depressed — produce positive risk-adjusted returns over a two-year ETH/USDC history?*

### 1.2 Market Assumptions

- The ETH/USDC 0.3% Uniswap V3 pool is the target instrument (pool address `0x8ad599c3a0ff1de082011efddc58f1908eb6e6d8`).
- Volatility is **mean-reverting**: periods of low realized volatility tend to be followed by higher volatility, creating a positive expectation for long gamma positions entered during calm markets.
- The pool's **streaming premium** — paid continuously by the long-option buyer to the short seller — is approximated by the pro-rated fee share of the buyer's liquidity position.
- Gas costs and protocol commissions are tunable parameters (set to zero in the base case; sensitivity tested in grid search).

### 1.3 Target Instruments

| Instrument | Role |
|---|---|
| ETH/USDC 0.3% Uniswap V3 pool | Data source + underlying |
| Panoptic long straddle | Primary strategy position |
| Passive Uniswap V3 LP (±10% range) | Baseline comparison |

### 1.4 Source of Alpha

The expected edge comes from two sources:
1. **Volatility risk premium (buy side):** entering long vol when realized IV is at its lower percentile captures the subsequent mean-reversion spike.
2. **Path-dependency of streaming premium:** the Panoptic pricing model (Eq. 1 below) means that options entered during low-activity periods accumulate premium slowly, improving the entry cost relative to Black-Scholes fair value.

---
