---
name: Get a calibrated crypto price forecast with risk context
description: >-
  Fetch the conformally-calibrated 80% price range for a symbol together with current risk
  state, and cross-check whale/on-chain flows. The calibrated range is the validated
  product; directional bias is context only.
api: openapi/crypto-market-signals-forecast-openapi-original.json
operations: [getKronosRisk, getKronosForecast, getCryptoWhaleAlerts]
---

# Calibrated crypto price forecast

## Payment
Each route answers `402` with an x402 challenge; pay USDC on Base and retry with
`PAYMENT-SIGNATURE`. No account or API key.

## Steps
1. **Risk state** — `getKronosRisk` (`GET /api/kronos/risk?symbol=ETH`, $0.02). Read the
   current market risk state and cooldown context before trusting a forecast.
2. **Forecast** — `getKronosForecast` (`GET /api/kronos/forecast?symbol=ETH`, $0.05).
   Use `range_80` (the ~0.80 empirical-coverage interval) as the product; `directional_bias`
   is supporting context only (~51% standalone accuracy per the provider).
3. **On-chain confirmation** (optional) — `getCryptoWhaleAlerts`
   (`GET /api/whale?chain=base`, $0.02) for whale/CEX/bridge/stablecoin flows. A `400`
   means an invalid `chain` or `lookback`.

## Rules
- Report the calibrated range, not a single point, and never emit a trade instruction.
- Record `response_schema_version` for reproducibility.
