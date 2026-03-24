# JTS Risk Review Log

Date Initialized: 2026-03-23

Use this log for all risk score changes, release-gate decisions, and formal acceptance records.

## 1) Decision Log

| Date | Review Mode | Decision ID | Risk ID(s) | Change Type | Previous Score/Gate | New Score/Gate | Decision Summary | Evidence Reviewed | Approved By (Role) | Acceptance Expiry | Next Review |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 2026-03-23 | Baseline Setup | DEC-2026-03-23-001 | JTS-R-001..JTS-R-030 | Initial Baseline | N/A | Initial scores set | Established initial 30-risk FMEA baseline and release-gate mapping | `risk_register.md`, `risk_register_plan.md`, repo evidence links in register | Program/Delivery Authority (pending formal sign-off) | N/A | 2026-03-24 |
| 2026-03-23 | Baseline Update | DEC-2026-03-23-002 | JTS-R-031..JTS-R-033 | Scope Expansion | N/A | Added 3 Critical/Block risks | Added systemic bias, fat-tail, and reference-class forecasting governance risks and KRIs | `risk_register.md`, `risk_register_plan.md`, `risk_scoring_rubric.md`, `risk_kri_catalog.md` | Program/Delivery Authority (pending formal sign-off) | N/A | 2026-03-24 |
| 2026-03-24 | Package Deep-Dive Update | DEC-2026-03-24-003 | JTS-R-003, JTS-R-004, JTS-R-006, JTS-R-008, JTS-R-010, JTS-R-012, JTS-R-018, JTS-R-020, JTS-R-027, JTS-R-034..JTS-R-039 | Rescore + Scope Expansion | V1.1 baseline (33 risks; 14 Block/Critical) | V1.2 baseline (39 risks; 17 Block/Critical) | Incorporated package-level application, infra, CI, and deploy-control evidence; adjusted existing risks and added six new package-derived risks | `risk_register.md`, `risk_kri_catalog.md`, `packages/hil-web-service`, `packages/jts-deploy`, `packages/ci`, `packages/scripts/end-to-end-tests` | Program/Delivery Authority (pending formal sign-off) | N/A | 2026-03-25 |

## 2) Formal Risk Acceptance Register

Any acceptance of unresolved `Critical`/`High` or `Severity=5` risk requires a row here.

| Acceptance ID | Risk ID | Accepted By (Role) | Reason for Acceptance | Compensating Controls | Approved On | Expiry Date | Re-Review Date | Escalation Trigger | Status |
|---|---|---|---|---|---|---|---|---|---|
| (none yet) | - | - | - | - | - | - | - | - | Open |

## 3) Review Cadence Checklist

### Pre-Release Hardening Mode (Daily)

- Validate all `Block` risks in `risk_register.md`.
- Review breached KRIs from `risk_kri_catalog.md`.
- Confirm no expired acceptance records.
- Confirm evidence freshness SLA is met for `Critical`/`High` risks.
- Review forecast-bias and tail-risk posture (`KRI-031` to `KRI-033`) and confirm reference-class coverage for release-significant estimates.

### BAU Mode

- Weekly: Critical/High risks.
- Biweekly: Medium risks.
- Monthly: full reprioritization.
- Monthly: forecast variance and tail-risk distribution review with reference-class recalibration.
- Quarterly: rubric calibration.

## 4) Change Control Notes

- Every score change requires rationale and evidence references.
- Every gate change (`Block`, `Conditional`, `Monitor`) requires approval record.
- Every acceptance expiry breach auto-escalates and returns risk to `Open`.

## 5) Natural Next Steps Checklist (Dated)

| Step | Action | Owner Role(s) | Planned Date | Status | Completion Evidence |
|---|---|---|---|---|---|
| 1 | Run cross-functional score calibration workshop and validate residual scores/gates | Engineering Lead, QA Lead, Security Lead, Operations Lead, Data Governance Lead | 2026-03-24 | Pending | Updated scores in `risk_register.md` + review entry |
| 2 | Resolve release-blocking acceptance decisions for unresolved `Block` risks | Program/Delivery Authority + required acceptance authorities | 2026-03-25 | Pending | Completed acceptance fields in `risk_register.md` and acceptance rows in this file |
| 3 | Collect live evidence for all `Block` risks (runtime, security, recovery) | Platform/DevOps Lead, Security Lead, Operations/Release Authority | 2026-03-26 | Pending | Evidence confidence/freshness updates in `risk_register.md` |
| 4 | Execute daily pre-release hardening review cycle | Risk Coordinator + risk owners | 2026-03-24 to release decision date | Pending | Daily decision rows in Section 1 |
| 5 | Re-run hard release-gate check and record go/no-go decision | Program/Delivery Authority + cross-functional risk board | 2026-03-28 | Pending | Explicit go/no-go decision entry in Section 1 |
| 6 | Produce reference-class forecast packet and independent challenge review for release-significant estimates | Program/Delivery Authority + Risk Coordinator + cross-functional risk board | 2026-03-27 | Pending | Added forecast basis, reference-class IDs, and percentile ranges for `JTS-R-031`..`JTS-R-033` |
| 7 | Execute package-control hardening plan for new risks (`JTS-R-034`..`JTS-R-039`) and publish mitigation evidence | App Engineering Lead, DevOps Lead, Security Lead, Data Governance Lead | 2026-03-29 | Pending | Updated mitigation status/evidence fields in `risk_register.md` plus decision entries in Section 1 |
