# Exchange Liquidity & Market Microstructure Research — BTCUSDT

> **Date:** 2026-06-14–15
> **Instrument:** BTCUSDT (spot and perp/USDⓈ-M)
> **Exchanges:** Binance, Bybit, OKX
> **Source:** proprietary measurements via public exchange REST APIs (klines, aggTrades, recent-trade, 24h-tickers)
> **Purpose:** calibration of unit economics for Open Trader project — where volume and trades concentrate, and the ceiling on executable volume per trader.
> **Authors:** Anna T, co-authored with l2xl

## 0. Key findings

1. **Volume concentrates in large tickets** (power-law): on spot the peak is at **$10k–$50k (33% of turnover on 1.6% of trades)**, on perp — **>$100k (44% on 0.9%)**. Trades <$100 are the majority by count but only ~1.5% of volume.
2. **Spot vs Perp are different markets.** On perp the whale tail is twice as thick, and there's no dust <$50 at all (minimum order size).
3. **Volume-weighted median trade size:** spot ~$15–20k, perp ~$70k.
4. **Active retail slice $100–$5k:** spot 27.4% of volume / 30.6% of trades; perp 8.8% of volume / 53.9% of trades.
5. **Ceiling on executable volume (V_max, 1% impact, 10-min window):** Binance ~$88k, Bybit ~$55k, OKX ~$42k. A $30k order is invisible everywhere (<1%).
6. **Effective take rate falls with ticket size** (VIP tiers) — but the entire retail ICP (≤$5k ticket) sits at VIP0 and pays full fees.

## 1. Methodology and sources

| Metric | Endpoint | Note |
|---|---|---|
| Turnover per window/day/month | `klines` / `kline` / `candles` | turnover/quoteVolume, USDT |
| Trade size distribution | Binance `aggTrades` | aggregated by aggressor order |
| Average trade size (snapshot) | `recent-trade` / `trades` | limit: Binance 1000, OKX 500, Bybit spot **60** |
| Full 24h turnover (all pairs) | `ticker/24hr` / `tickers` | USD-stable pairs only |

**Caveats:** histograms are a single window on a single exchange (Binance), not time-averaged; the average trade size for Bybit/OKX is a snapshot, not a monthly average (only Binance's candles give the exact trade count). Data was collected locally.

## 2. Turnover by exchange — BTCUSDT spot (30 full days)

| Exchange | 30d turnover | /day | /1h window | /10m window | share |
|---|---:|---:|---:|---:|---:|
| Binance | $37.86B | $1.262B | $52.59M | $8.76M | 47% |
| Bybit | $23.60B | $786.8M | $32.78M | $5.46M | 29% |
| OKX | $18.33B | $610.8M | $25.45M | $4.24M | 23% |

Turnover ranking: Binance ≈ 1.6× Bybit ≈ 2.1× OKX.

## 3. Average trade size (with caveats)

| Exchange | Mean (month)* | Mean (snapshot) | Median (snapshot) | Sample |
|---|---:|---:|---:|---:|
| Binance | **$346** | $364 | $5 | 1000 |
| Bybit | n/a | $2,574 | $426 | **60** ⚠️ |
| OKX | n/a | $892 | $13 | 500 |

\* The exact monthly average (turnover ÷ trade count) is only available for Binance. Bybit n=60 is statistically unreliable. **Mean ≫ median everywhere** — a heavy-tailed distribution; "average trade size" does not reflect where the volume is (see §4).

## 4. Volume distribution by trade size — Binance spot vs perp

Samples: **spot** — 50,000 aggTrades, ~129 min, turnover $53.32M; **perp** — 50,000 aggTrades, ~54 min, turnover $251.23M.

| Trade size | Spot % of volume | Perp % of volume | Spot % of trades | Perp % of trades |
|---|---:|---:|---:|---:|
| <$10 | 0.11% | 0.00% | 18.04% | 0.00% |
| $10–$50 | 0.77% | 0.00% | 38.78% | 0.00% |
| $50–$100 | 0.59% | 0.45% | 8.95% | 34.45% |
| $100–$500 | 3.16% | 1.40% | 13.99% | 33.83% |
| $500–$1k | 3.81% | 0.95% | 5.75% | 6.44% |
| $1k–$5k | 20.46% | 6.47% | 10.86% | 13.62% |
| $5k–$10k | 10.79% | 5.48% | 1.70% | 3.95% |
| $10k–$50k | 32.70% | 25.42% | 1.61% | 5.65% |
| $50k–$100k | 15.32% | 15.88% | 0.23% | 1.16% |
| >$100k | 12.29% | 43.96% | 0.08% | 0.89% |

Perp differences: (1) no dust <$50 (minimum order size); (2) the >$100k tail is twice as thick; (3) the volume's center of mass is much higher (volume-weighted median ~$70k vs ~$15–20k on spot).

## 5. Depth and the ceiling on executable volume (V_max)

Volume ceiling per window at an impact threshold of ~1% (on monthly average turnovers, §2):

| Exchange | V_max / 10 min | V_max / 1 hour |
|---|---:|---:|
| Binance | ~$87.6k | ~$526k |
| Bybit | ~$54.6k | ~$328k |
| OKX | ~$42.4k | ~$254k |

A $30k order is 0.34% / 0.55% / 0.71% of the 10-min window — invisible. **Monetizable volume per trader is bounded not by deposit size but by this ceiling.**

## 6. Spot vs Perp by exchange (24h, all USD pairs)

| Exchange | Spot | Perp | perp/spot | spot share |
|---|---:|---:|---:|---:|
| Binance | $7.90B | $57.71B | 7.3× | 12.0% |
| OKX | $1.38B | $26.44B | 19.2× | 4.9% |
| Bybit | $2.26B | $12.51B | 5.5× | 15.3% |
| Gate.io | $1.29B | $6.78B | 5.2× | 16.0% |
| BingX | $0.36B | $5.41B | 14.8× | 6.3% |
| KuCoin | $0.80B | $1.45B | 1.8× | 35.6% |
| Crypto.com | $0.54B | $0.38B | 0.7× | 59.1% |
| Bitfinex | $0.10B | $0.02B | 0.2× | 81.9% |
| **Total (8 exchanges)** | **$14.63B** | **$110.70B** | **7.6×** | **11.7%** |

**Market-wide average (8 exchanges, turnover-weighted): spot : derivatives ≈ 12 : 88, perp ≈ 7.6× spot.** Derivatives dominate. A simple equal-weighted average gives 29% spot — but this is **distorted** by small spot-skewed exchanges (Bitfinex $0.1B, Crypto.com) that barely register in dollar terms; for a "market-wide average" use the weighted aggregate (12% spot).

**Excluded from the aggregate: Bitget and HTX** — measurement artifacts via ccxt (Bitget spot inflated by the `base×last` fallback; HTX linear-swap undercounted). Both are perp-skewed by profile → correcting them properly would shift the aggregate toward **more** perp, i.e. 88% is a lower-bound estimate.

Measured only across USD-stable pairs (USDT/USDC/FDUSD…), June 2026 snapshots, different windows. Binance spot was **adjusted**: the raw $355B figure was an artifact of summing `quoteVolume` across pairs quoted in different currencies (weak fiats ARS ~1000:1, TRY ~32:1); filtering to stable pairs gave $7.90B. Sanity check: BTCUSDT spot $1.26B/day = ~16% of Binance spot — consistent. Scripts: `spot_perp_ratio.py` (Binance/OKX/Bybit, native APIs), `spot_perp_7.py` (the rest, via CCXT).

## 7. Implications for unit economics

- **Revenue lives in $1k+ tickets** → the unit is average trade size, not deposit size.
- **Retail core $100–$5k** — full-fee, VIP0, winnable; the basis for the `fee-share-model.md` §11 model.
- **Perp ≠ spot for our ICP:** on perp, 60% of volume comes from tickets >$50k (pro/VIP players outside the ICP); retail addresses ~40% of perp vs ~71% of spot.
- **Market capacity is not a binding constraint:** our platform's turnover is a tiny fraction of a percent of the market.

## Appendix. Scripts

Local scripts used (run outside the work environment, where egress is open):

| Script | Purpose |
|---|---|
| `monthly_stats.py` | 30-day turnover + average trade size (3 exchanges) |
| `vol_by_size.py` / `vol_by_size_perp.py` | volume-by-trade-size histogram (Binance spot/perp) |
| `exchange_daily.py` | full 24h turnover across all USD pairs (Binance/Bybit) |
