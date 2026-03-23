# Risk Register and Release Governance Plan (FMEA)

## 1. Objective

Create a living, evidence-backed risk register that is directly tied to release decisions.

This plan is not only for collecting risks. It defines:

- how risk affects go/no-go decisions,
- who can accept residual risk,
- what evidence is required before release,
- and how risks are re-scored as controls are implemented.

## 2. Scope and Boundaries

### In Scope

- Technical/software risks: application workflows, data pipeline, CI/CD, infrastructure, deployment, observability, security.
- Governance/process risks: QA completeness, documentation drift, release control quality, compliance evidence quality, role clarity, and knowledge concentration.
- External dependency risks: AWS/service dependencies, self-hosted runner dependency, cross-repo dependency.

### Out of Scope

- Enterprise budget and contract transfer strategy.
- Risks unrelated to this repository and its operating workflows.

## 3. Release Decision Policy (Hard Gates)

### 3.1 Risk Appetite Statement

For production release:

- No unowned mission-critical risk is acceptable.
- No unexpired, undocumented exception is acceptable.
- Document-only evidence is insufficient for high-consequence controls.

### 3.2 Formal Go/No-Go Rules

Production release is blocked if any condition is true:

1. Any open risk with `Residual Severity = 5` without formal signed acceptance.
2. Any open `Critical` risk (`Residual RPN >= 60`) without formal signed acceptance.
3. Any open `High` risk (`Residual RPN 40-59`) without either:
   - completed mitigation, or
   - approved compensating controls plus expiry-bounded acceptance.
4. Any risk acceptance record is expired.
5. Required runtime evidence class (Section 5) is missing for a `Critical` or `High` risk.
6. Any release-significant estimate or plan package lacks reference-class forecasting evidence (outside view) with percentile ranges (`P50`, `P80`, `P95`).

### 3.3 Acceptance Authority

Risk acceptance must be role-based and explicit:

- Security/compliance risks: Cloud Security Architect + Program/Delivery authority.
- Clinical/data-integrity risks: Data Governance/Clinical owner + Program/Delivery authority.
- Operational resilience risks: Platform/Ops owner + Program/Delivery authority.

Every acceptance must include:

- accepted-by role,
- acceptance reason,
- compensating controls,
- expiry date,
- re-review date,
- and rollback/escalation trigger.

## 4. FMEA Scoring Model

### 4.1 Scores and Formula

- Use 1-5 scales for `Severity (S)`, `Occurrence (O)`, and `Detection (D)`.
- Compute both inherent and residual scores.
- `Inherent RPN = Inherent S x Inherent O x Inherent D`
- `Residual RPN = Residual S x Residual O x Residual D`

### 4.2 Priority Tiers

- `Critical`: `Residual RPN >= 60` or any `Residual S = 5`
- `High`: `Residual RPN 40-59`
- `Medium`: `Residual RPN 20-39`
- `Low`: `Residual RPN < 20`

Severity override applies: `S=5` is always release-significant.

### 4.3 Additional Required Risk Attributes

Each risk must include:

- Inherent S/O/D/RPN
- Residual S/O/D/RPN
- Control Effectiveness (`1-5`, where `1` is weak and `5` is strong)
- Evidence Confidence (`High`, `Medium`, `Low`)
- Evidence Date (last verified date)
- Evidence Freshness SLA (max age allowed)
- Forecast Basis (`Inside View`, `Outside View`, or `Hybrid`) for estimate-sensitive risks
- Reference Class Record (historical comparator set ID and sample size)
- Percentile Forecast Range (`P50`, `P80`, `P95`) for release-significant schedule/cost assumptions

## 5. Evidence Model (Weighted by Trust)

Evidence is required across classes. For `Critical` and `High` risks, design/document evidence alone does not close risk.

| Evidence Class | Examples | Minimum Requirement |
|---|---|---|
| Design | architecture docs, plans, standards | context only; never sole closure evidence for Critical/High |
| Implementation | code, IaC, workflow config | required for all software/control claims |
| Test | unit/integration/E2E results, CI history | required for behavior claims |
| Runtime | alarms, deploy history, incidents, logs/metrics coverage | required for production readiness claims |
| Security | vulnerability scan results, access review evidence, policy checks | required for security/compliance claims |
| Recovery | rollback drill, restore test, backup verification | required for resilience and recoverability claims |
| Governance | approvals, review records, acceptance log | required for release decisions and exceptions |

Evidence confidence rules:

- `High`: implementation + recent test/runtime proof + traceable owner review.
- `Medium`: implementation + test proof, limited runtime proof.
- `Low`: design/doc only, stale proof, or conflicting sources.

## 6. Taxonomy and Crown-Jewel Failure Chains

### 6.1 Risk Categories

1. Application Workflow Integrity
2. Data Pipeline and Quality
3. Security and Access Control
4. Infrastructure and Deployment
5. CI/CD and Test Assurance
6. Observability and Incident Readiness
7. Recovery and Rollback
8. Governance and Compliance Controls
9. Documentation and Operational Readiness
10. External Dependencies and Vendor Concentration
11. Forecasting and Estimation Governance

### 6.2 Mandatory Scenario View

In addition to row-level risks, maintain scenario chains for crown-jewel failures:

1. Ingest trigger failure to delayed/failed processing.
2. Review-gate deadlock or ambiguous workflow hold.
3. Partial DynamoDB/Step Functions state leading to inconsistency.
4. Stale or race-prone lock behavior causing edit collisions.
5. OCR/NLP quality drift causing downstream data integrity issues.
6. Failed rollback/restore after bad deployment.
7. Biased or inside-view planning assumptions leading to tail overrun and unsafe release pressure.

Each scenario must list initiating event, intermediate control points, detection path, and containment action.

## 7. Workstreams, Roles, and Decision Owners

| Workstream | Focus | Analysis Owner Role | Decision Owner Role |
|---|---|---|---|
| A: Architecture/Infra/Ops | deployment, runtime, infra, dependency risk | Platform/DevOps Lead | Operations/Release Authority |
| B: Application/Data | HIL, lock semantics, workflow integrity, OCR/NLP, data quality | Application Engineering Lead | Data Governance + App Lead |
| C: Governance/Security/QA | QA controls, compliance evidence, release process | QA Lead + Security Lead | Program/Delivery Authority |
| D: Synthesis/Scoring | dedupe, normalize, score, prioritize | Risk Coordinator | Cross-functional risk board |
| E: Forecast Governance | estimate quality, reference class calibration, tail-risk reporting | Risk Coordinator + Program/Delivery Authority | Program/Delivery Authority + cross-functional risk board |

Collection lanes are not enough. Each lane has named role accountability for decision and acceptance.

## 8. Register Template (Required Fields)

Each risk row must include all fields below:

| Field | Description |
|---|---|
| Risk ID | Unique identifier (for example `JTS-R-001`) |
| Category | Taxonomy category |
| Failure Mode | What fails |
| Cause | Why it fails |
| Effect | Business/operational/compliance impact |
| Affected Asset/Workflow | Service/process impacted |
| Current Controls | Existing preventive/detective controls |
| Inherent S/O/D | Inherent score values |
| Inherent RPN | Inherent S x O x D |
| Control Effectiveness | 1-5 control strength |
| Residual S/O/D | Residual score values |
| Residual RPN | Residual S x O x D |
| Priority Tier | Critical/High/Medium/Low |
| Owner Role | Responsible role |
| Mitigation Plan | Preventive actions |
| Contingency Plan | Response when risk occurs |
| KRI | Metric used to monitor risk |
| Trigger Threshold | Breach condition that escalates |
| Forecast Basis | Inside view/outside view designation for estimate assumptions |
| Reference Class ID | Historical comparator dataset reference used for estimate calibration |
| Forecast Percentiles | Documented `P50`/`P80`/`P95` values for release-significant assumptions |
| Evidence Class Coverage | Which classes are present |
| Evidence Date | Last validation date |
| Evidence Confidence | High/Medium/Low |
| Release Gate Impact | `Block`, `Conditional`, or `Monitor` |
| Accepted By | Role approving residual risk (if any) |
| Acceptance Expiry | Date acceptance expires |
| Status | Open/In Progress/Mitigated/Accepted/Closed |
| Target Date | Mitigation due date |
| Evidence | File, system, and line/record references |

## 9. Execution Phases

### Phase 0: Governance Setup (Day 0)

- Confirm release gate policy and acceptance authority map.
- Approve rubric and evidence confidence rules.
- Finalize templates.

Exit criteria:

- Go/no-go policy approved.
- Acceptance workflow approved.

### Phase 1: Immediate Baseline (Day 1)

- Build first 10 high-value risks from known evidence.
- Score inherent and residual values.
- Assign owners and gate impact.

Exit criteria:

- Top 10 baseline register published.
- All top 10 have owner, mitigation, contingency, KRI, trigger.

### Phase 2: Full Inventory and Scenario Mapping (Days 2-3)

- Expand to full category coverage.
- Build crown-jewel scenario chains.
- Remove duplicates and normalize statements.

Exit criteria:

- Full risk set normalized.
- Mandatory scenario chains completed.

### Phase 3: Live Evidence Collection (Days 3-5)

- Collect runtime and security evidence (not only repository artifacts).
- Verify alarms, deploy history, scan trends, restore/rollback evidence.

Exit criteria:

- Evidence classes satisfied for Critical/High risks.
- Evidence confidence updated.

### Phase 4: Calibration and Treatment Planning (Days 5-6)

- Cross-functional scoring workshop.
- Confirm mitigation backlog, due dates, and acceptance decisions.

Exit criteria:

- Final baseline scored and prioritized.
- Release blockers explicitly identified.

### Phase 5: Release Readiness Decision and BAU Governance (Day 7+)

- Apply hard gates for release readiness.
- Move to recurring review cadence.

Exit criteria:

- Release decision recorded with traceable evidence.
- Ongoing governance cadence active.

## 10. Thoroughness and Quality Gates

Baseline quality gates:

- No uncited risks: every row has at least one traceable evidence reference.
- No placeholder closure: design docs alone cannot close Critical/High risk.
- No unowned high-consequence risk: all Critical/High risks have owner role and due date.
- No stale evidence: Critical/High evidence must meet freshness SLA.
- No inside-view-only release assumptions: release-significant estimates must include reference class evidence and `P50`/`P80`/`P95` ranges.
- No average-only reporting: tail-risk distribution view (`>50%` and `>100%` overrun incidence) is mandatory for planning-governance decisions.
- No filler category coverage: scenario-chain coverage is mandatory, not just risk counts.

## 11. Pre-Release and BAU Cadence

### Pre-Release Hardening Mode

- Daily: review all Critical/High risks until release decision.
- Daily: review newly breached KRIs and open escalations.
- Daily: verify acceptance expiries and blocker status.
- Daily: review forecast-bias and tail-risk KRIs (`KRI-031` to `KRI-033`) for release-significant work packages.

### BAU Mode

- Weekly: Critical/High review.
- Biweekly: Medium review.
- Monthly: full reprioritization and trend review.
- Monthly: refresh reference-class forecast dataset and validate forecast variance trends.
- Quarterly: rubric and control-effectiveness calibration.

## 12. Backlog and Security Debt Gating

The risk process must explicitly include static analysis and vulnerability debt posture.

Minimum governance checks before production decision:

- Open critical vulnerabilities: none without formal acceptance.
- Major vulnerabilities and critical code-quality findings: must have dated remediation or approved exception.
- Any exception must include owner, compensating controls, and expiry.

### 12.1 Forecasting and Tail-Risk Gating

Before production release decision, forecasting governance must include:

- Outside-view evidence using reference class forecasting for all release-significant estimates.
- Documented comparator set (historical sample and rationale) and uplift assumptions.
- Percentile forecast ranges (`P50`, `P80`, `P95`) with explicit contingency reserve.
- Independent challenge review separate from sponsor/authoring team.
- Tail-risk review (`KRI-032`) in addition to average variance metrics.

## 13. Initial Evidence Sources

Repository sources (starting point, not sufficient alone):

- `complexity_analysis.md`
- `complexity_analysis_exec_summary.md`
- `docs/software-qa.md`
- `docs/ops-platform-guide.md`
- `docs/design/system-design-doc.md`
- `docs/data-quality-management.md`
- `docs/ui/HIL_Lock_Feature.md`
- `.github/workflows/main.yml`
- `.github/PULL_REQUEST_TEMPLATE.md`
- `packages/scripts/end-to-end-tests/IMPLEMENTATION.md`
- `packages/scripts/end-to-end-tests/docs/multi-form-phase2-2026-02-24.md`
- `issues.json`

Runtime/governance evidence to collect during Phase 3:

- CI pass/fail history for release-relevant workflows.
- Deployment success/failure trend and rollback outcomes.
- Alarm coverage matrix and alert response evidence.
- Backup/restore drill results.
- Access review records and vulnerability aging trends.
- Forecast versus actual variance records for release-significant work packages.
- Reference-class dataset and calibration notes used for outside-view estimates.
- Independent challenge records for sponsor-provided estimates/benefit claims.

## 14. Deliverables

- `risk_register_plan.md` (this governance plan)
- `risk_register.md` (baseline register)
- `risk_scoring_rubric.md`
- `risk_kri_catalog.md`
- `risk_review_log.md`

## 15. Definition of Done (Baseline)

Baseline is complete when:

- Top 10 risks are scored with inherent and residual FMEA values.
- Release gate impact is assigned for each top risk.
- Formal owners are assigned for all Critical/High risks.
- Evidence confidence and freshness are recorded for all Critical/High risks.
- Acceptance records exist for any unresolved release-significant risks.
- Reference-class and percentile forecast evidence is recorded for estimate-sensitive release decisions.
- A release decision can be traced to explicit gate outcomes.

## 16. Natural Next Steps

Execute the following in order:

1. Run a cross-functional score calibration workshop.
   - Participants: Engineering, QA, Security, Operations, and Data Governance owners.
   - Output: validated residual scores and release-gate impacts in `risk_register.md`.

2. Resolve release-blocking acceptance decisions.
   - For any unresolved `Block` risk that will not be mitigated before release, record formal acceptance.
   - Output: completed `Accepted By`, `Acceptance Expiry`, and acceptance rationale in `risk_register.md` and `risk_review_log.md`.

3. Collect live operational evidence for all `Block` risks first.
   - Focus: runtime alarms, deploy history, restore/rollback drill results, vulnerability-aging data, and access-review evidence.
   - Output: updated evidence classes, confidence, and freshness timestamps in `risk_register.md`.

4. Enforce daily pre-release hardening cadence until go/no-go.
   - Review all `Block` and `Conditional` risks, KRI breaches, and acceptance expiries each day.
   - Output: daily decision entries in `risk_review_log.md`.

5. Re-run release gate check and issue decision record.
   - Apply Section 3 hard gates using the latest evidence and risk states.
   - Output: explicit go/no-go decision with traceable rationale in `risk_review_log.md`.

6. Complete forecast-governance packet for all release-significant estimates.
   - Include reference class evidence, `P50`/`P80`/`P95` ranges, and tail-risk contingency assumptions.
   - Output: documented forecast basis fields and linked evidence for `JTS-R-031` to `JTS-R-033` in `risk_register.md` and `risk_review_log.md`.
