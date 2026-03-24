# JTS Risk Scoring Rubric (FMEA)

Date: 2026-03-23

## 1) Purpose

This rubric standardizes scoring for JTS release governance so risk ratings are consistent, auditable, and tied to release decisions.

Use this rubric with:

- `risk_register.md`
- `risk_register_plan.md`
- `risk_review_log.md`

## 2) Scoring Formula

- Inherent RPN = Inherent Severity x Inherent Occurrence x Inherent Detection
- Residual RPN = Residual Severity x Residual Occurrence x Residual Detection
- Scale for each dimension: 1 (best/lowest risk) to 5 (worst/highest risk)

Severity override rule:

- Any residual score with `Severity = 5` is release-significant even if RPN is lower than other items.

## 3) Severity (S)

| Score | Definition | Examples in JTS Context |
|---|---|---|
| 1 | Negligible impact | Cosmetic issue; no service/data impact |
| 2 | Minor impact | Limited degradation; workaround available; no compliance exposure |
| 3 | Moderate impact | Measurable service/process degradation; non-trivial rework |
| 4 | Major impact | Significant operational disruption, potential compliance/security concern |
| 5 | Mission-critical impact | Prolonged outage, critical data-integrity loss, or regulatory/privacy breach |

## 4) Occurrence (O)

| Score | Definition | Anchor Guidance |
|---|---|---|
| 1 | Rare | Not observed in normal operations; edge-case only |
| 2 | Uncommon | Possible under specific conditions |
| 3 | Occasional | Seen intermittently or expected in some releases |
| 4 | Frequent | Recurrent or strongly suggested by current evidence |
| 5 | Very Frequent | Persistently recurring or highly probable |

## 5) Detection (D)

Scored as detectability before impact. Higher score means harder to detect in time.

| Score | Definition | Detection Posture |
|---|---|---|
| 1 | Very high detectability | Strong automated controls catch issue early and consistently |
| 2 | High detectability | Mostly caught by tests/alerts with low escape rate |
| 3 | Moderate detectability | Mixed automation/manual; meaningful escape risk remains |
| 4 | Low detectability | Mostly manual checks or sparse instrumentation |
| 5 | Very low detectability | Likely discovered only after operational impact |

## 6) Control Effectiveness (1-5)

| Score | Interpretation |
|---|---|
| 1 | Weak or absent controls |
| 2 | Partial controls with major gaps |
| 3 | Reasonable controls with known limitations |
| 4 | Strong controls with minor gaps |
| 5 | Robust controls demonstrated with fresh evidence |

## 7) Evidence Confidence and Freshness

### 7.1 Confidence

| Level | Criteria |
|---|---|
| High | Implementation + test/runtime proof + traceable owner review |
| Medium | Implementation + test proof, limited runtime proof |
| Low | Design/document evidence only, stale proof, or conflicting evidence |

### 7.2 Freshness SLA

| Risk Priority | Maximum Evidence Age |
|---|---|
| Critical | 14 days |
| High | 30 days |
| Medium | 60 days |
| Low | 90 days |

If freshness SLA is exceeded, risk cannot be considered closed.

## 8) Priority and Gate Mapping

| Residual Condition | Priority | Default Release Gate Impact |
|---|---|---|
| RPN >= 60 or Severity = 5 | Critical | Block |
| RPN 40-59 | High | Conditional or Block (board decision) |
| RPN 20-39 | Medium | Conditional or Monitor |
| RPN < 20 | Low | Monitor |

Hard rule: unresolved Critical risks require formal acceptance with expiry and compensating controls.

## 9) Calibration Procedure

1. Score inherent risk before considering controls.
2. Document current controls and evidence classes.
3. Score residual risk with evidence confidence.
4. Assign release gate impact (`Block`, `Conditional`, `Monitor`).
5. Record rationale in `risk_review_log.md` for any score changes.

## 10) Tie-Breaker Rules

When two risks have similar residual RPN:

1. Higher Severity wins priority.
2. Lower detectability (higher D) wins priority.
3. Lower evidence confidence wins priority.

## 11) Bias and Forecast Governance Adjustments

Apply these adjustments for planning and forecast-driven risks (especially `JTS-R-031` to `JTS-R-033`):

- Missing reference class forecast for a release-significant estimate:
  - Detection floor = 4
  - Occurrence floor = 4
  - Evidence confidence cannot exceed `Medium`
- No independent challenge of sponsor-provided estimate or benefit case:
  - Increase Occurrence by `+1` (cap at 5)
- Average-only reporting (no percentile view and no tail analysis):
  - Release gate impact cannot be `Monitor`
  - If residual `Severity >= 4`, default gate is `Block` until tail controls are approved

Black swan watchlist trigger:

- Any forecast/actual overrun `>100%` or repeated `>50%` overruns over two cycles requires explicit risk-board review and contingency re-baseline before go/no-go.

## 12) Acceptance Rules

A risk may be accepted only if all are present:

- Accepted by authorized role(s)
- Explicit acceptance reason
- Compensating controls
- Expiry date
- Re-review date
- Escalation trigger

Expired acceptance automatically returns risk to `Open` and `Conditional/Block` state.
