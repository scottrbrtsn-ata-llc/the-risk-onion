# The Risk Onion

The Risk Onion is named for the layers of the architect elevator, from the C-suite in the penthouse down to the boiler room.

Risk exists at every level in a software project. It lives in an open system where people, technology, delivery process, organizational structure, business sector, and customer reality collide to form a complex ecosystem of value.

The Risk Onion is a layered approach to exposing that risk. If you peel it honestly, it will likely cause tears. Better ours now than our customers' later.

## What This Repository Is

This repository is a documentation-first risk governance baseline for software delivery and release readiness.

It combines:

- FMEA-style scoring (inherent and residual)
- Hard release gates and explicit acceptance authority
- Evidence confidence and freshness rules
- KRI (Key Risk Indicator) monitoring
- Forecast governance controls (reference class, percentile ranges, and tail-risk checks)

## Repository Contents

| File | Purpose |
|---|---|
| `risk_register_plan.md` | Governance operating model, release gate policy, ownership model, and execution phases |
| `risk_scoring_rubric.md` | Scoring definitions for Severity/Occurrence/Detection, priority tiers, and acceptance rules |
| `risk_register.md` | Primary human-readable risk register baseline |
| `risk_register_v0.csv` | Machine-readable export of the baseline register |
| `risk_kri_catalog.md` | KRI formulas, thresholds, cadence, ownership, and linked risks |
| `risk_review_log.md` | Decision log, acceptance register, and review cadence checklist |
| `complexity_analysis_exec_summary.md` | Executive summary of complexity and churn risk |
| `complexity_analysis.md` | Full complexity/churn hotspot analysis and reduction plan |
| `bottleneck_risk_calculator.md` | Delivery bottleneck model for capacity, review pressure, and merge risk |
| `scc.txt` | Repository language/complexity snapshot |
| `churn.txt` | Historical file-touch data used in churn analysis |

## Read Order

Recommended sequence for new readers:

1. `risk_register_plan.md`
2. `risk_scoring_rubric.md`
3. `risk_register.md`
4. `risk_kri_catalog.md`
5. `risk_review_log.md`
6. `complexity_analysis_exec_summary.md`
7. `complexity_analysis.md`

## Baseline Snapshot (2026-03-23)

From `risk_register.md`:

- Total risks: 33
- Critical: 14
- High: 4
- Medium: 15
- Low: 0
- Current release blockers (`Block`): 14

This baseline is intentionally strict: unresolved critical and high-consequence risks are treated as release-significant.

## Core Governance Principles

- No unowned mission-critical risk
- No expired, undocumented acceptance
- No closure of Critical/High risk with document-only evidence
- No release-significant estimate without outside-view/reference-class evidence
- No average-only forecast reporting when tail risk can threaten release outcomes

## Operating Cadence

### Pre-release hardening mode

- Daily review of all `Block` and `Conditional` risks
- Daily KRI breach review
- Daily acceptance-expiry verification
- Daily forecast-bias and tail-risk review (`KRI-031` to `KRI-033`)

### BAU mode

- Weekly: Critical and High risks
- Biweekly: Medium risks
- Monthly: full reprioritization and trend review
- Quarterly: rubric and control-effectiveness calibration

## How To Maintain This Repository

1. Add or revise risks in `risk_register.md` (and `risk_register_v0.csv` when needed).
2. Re-score using `risk_scoring_rubric.md`.
3. Update thresholds/owners in `risk_kri_catalog.md` when controls change.
4. Record all score, gate, and acceptance decisions in `risk_review_log.md`.
5. Re-run release hard-gate checks before go/no-go decisions.

## Audience

- Delivery leadership and risk boards
- Engineering, QA, Security, and Operations leads
- Program governance stakeholders
- Teams accountable for release readiness decisions

## License

See `LICENSE`.
