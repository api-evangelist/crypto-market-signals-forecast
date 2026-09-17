---
name: Run a signal-safety check before acting on a crypto signal
description: >-
  Screen a proposed crypto market signal for anomalies, calibrate its confidence, and run a
  preflight verification — the ForgeMesh signal-safety flow. Market intelligence only.
api: openapi/crypto-market-signals-forecast-openapi-original.json
operations: [getCatalog, runAnomaly, runCalibration, runVerification]
---

# Signal-safety check

## Payment
Each POST route answers `402` with an x402 challenge; pay USDC on Base and retry with
`PAYMENT-SIGNATURE`. `400` means an invalid payload — fix and retry.

## Steps
1. **Catalog** (optional) — `getCatalog` (`GET /api/catalog`, $0.005) to list the
   anomaly, calibration and verification products.
2. **Anomaly review** — `runAnomaly` (`POST /api/anomaly`, $0.07). Body requires `symbol`
   and a `features` object (price_change, volume_change, volatility, …). Read
   `anomaly_score`, `anomaly_level`, `review_label`, `drivers`, `agreement_score`,
   `sample_size`.
3. **Calibrate confidence** — `runCalibration` (`POST /api/calibration`, $0.05). Converts a
   raw confidence to `calibrated_confidence` with `confidence_band`, `historical_hit_rate`,
   `calibration_grade`. Prefer `directional_bias`; `signal_type` is a legacy alias.
4. **Verify** — `runVerification` (`POST /api/verify`, $0.05) for a preflight review of an
   autonomous decision-support workflow. `proposed_action` is a legacy directional-context
   alias, not an instruction.

## Rules
- All outputs are market intelligence; `legacy_fields.recommended_action` is compatibility
  only and is not an instruction to trade, transact, hold, rebalance, or take market activity.
