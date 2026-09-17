---
name: Make and audit a journaled crypto market-intelligence decision
description: >-
  Produce an auditable, calibrated market-intelligence decision for a symbol, capture its
  decision_id, then later audit the recorded outcome versus real prices. Market
  intelligence only — no output is a trade instruction.
api: openapi/crypto-market-signals-forecast-openapi-original.json
operations: [getKronosPreflight, getKronosDecision, getKronosAudit]
---

# Journaled crypto market-intelligence decision

This API is **market intelligence only**. Every response carries
`compliance_mode=market_intelligence_only`; treat `directional_bias` as context, never as
an instruction to trade, transact, hold or rebalance.

## Payment
No API key or account. Each call returns HTTP `402` with an x402 challenge; settle the
USDC micropayment on Base (eip155:8453) and retry with the `PAYMENT-SIGNATURE` header. Use
the optional x402 `payment-identifier` for retry-safety.

## Steps
1. **Preflight** — `getKronosPreflight` (`GET /api/kronos/preflight?symbol=BTC`, $0.05).
   Read `risk_state`, `review_level`, `directional_bias` and `observation_thresholds` to
   decide whether context warrants a full decision.
2. **Decide + journal** — `getKronosDecision` (`GET /api/kronos/decision?symbol=BTC`,
   $0.15). Store the returned **`decision_id`** — it is the audit key. The response bundles
   directional_bias, anomaly scoring, calibration, verification and liability disclaimers.
3. **Audit later** — `getKronosAudit` (`GET /api/kronos/audit?decision_id=...`, $0.07)
   compares the journaled context against realized prices. A `404` means the `decision_id`
   is unknown — verify it was captured in step 2.

## Rules
- Handle `402` as the payment step, not an error; `400` means a bad parameter; `404` on
  audit means an unknown `decision_id`.
- Persist `decision_id` and `response_schema_version` alongside your own record.
