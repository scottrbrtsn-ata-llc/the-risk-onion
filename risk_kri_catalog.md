# JTS KRI Catalog

Date: 2026-03-23

This catalog defines measurable key risk indicators (KRIs) used by `risk_register.md`.

| KRI ID | Metric | Definition / Formula | Data Source | Threshold (Escalation Trigger) | Cadence | Owner Role | Linked Risks |
|---|---|---|---|---|---|---|---|
| KRI-001 | With-review determinism | `% release runs with deterministic with-review outcome` | E2E reports/logs | Any release run not deterministic | Daily (pre-release) | QA Lead | JTS-R-001 |
| KRI-002 | Lock contention and expiry | `lock_conflicts / edit_attempts` and `expiry_interruptions / sessions` | App metrics + logs | Conflicts >2% or expiry interruptions >1% weekly | Daily/Weekly | App Engineering Lead | JTS-R-002 |
| KRI-003 | PR release-path E2E coverage | `% release-scoped PRs with required E2E smoke` | CI workflow history | Coverage <100% | Daily (pre-release) | QA Lead + DevOps Lead | JTS-R-003 |
| KRI-004 | QA claim-to-gate drift | `count(documented controls not enforced)` | QA doc review + workflow checks | Any mismatch for release-significant control | Weekly | QA Lead | JTS-R-004 |
| KRI-005 | Critical doc completeness | `% required sections complete with non-placeholder content` | Governance docs review | <100% at release checkpoint | Weekly | Program/Delivery Authority | JTS-R-005 |
| KRI-006 | NLP/OCR quality stability | Precision/recall against benchmark corpus | QA benchmark runs | Threshold undefined or below approved bounds | Weekly (pre-release) | Data Governance Lead | JTS-R-006 |
| KRI-007 | HIL deploy path reliability | `% successful HIL deploy steps in release-path runs` | GitHub Actions job history | <99% success | Daily/Weekly | DevOps Lead | JTS-R-007 |
| KRI-008 | Critical control observability coverage | `% critical workflow controls with validated alarms + runbooks` | Alarm matrix + runbook checks | <100% coverage | Weekly | Ops Lead | JTS-R-008 |
| KRI-009 | Restore/rollback proof | `% scheduled restore drills passing within SLA` | Recovery drill records | Any failed/missing drill in window | Weekly/Monthly | Ops/Release Authority | JTS-R-009 |
| KRI-010 | Critical vuln exposure | `count(open critical vulnerabilities)` | Security scan outputs | >0 without formal acceptance | Daily/Weekly | Security Lead | JTS-R-010 |
| KRI-011 | Test-path parity | `count(release evidence items derived from test-only approval flow)` | E2E process audit | >0 for release sign-off | Per release | QA Lead | JTS-R-011 |
| KRI-012 | Multi-form E2E readiness | `% supported forms with full release-path E2E pass` | E2E run summaries | <100% for release scope | Weekly | QA Lead | JTS-R-012 |
| KRI-013 | Review-gate re-entry incidence | `count(with-review re-entry events)` | Step Functions/E2E diagnostics | >0 in release candidate runs | Daily (pre-release) | App Engineering Lead | JTS-R-013 |
| KRI-014 | Automation maturity index | `% planned automation controls implemented` | Ops governance tracker | <target milestone for release phase | Biweekly | Program/Delivery Authority | JTS-R-014 |
| KRI-015 | Governance TODO debt | `count(TODO/TBD in release-significant sections)` | Governance docs scan | >0 at release checkpoint | Weekly | Program/Delivery Authority | JTS-R-015 |
| KRI-016 | Transfer dependency latency | `median DCOP transfer lead time` | Ops handoff logs | Above agreed release SLA | Weekly | Ops Lead | JTS-R-016 |
| KRI-017 | Runbook correctness defects | `count(validated command/runbook defects)` | Runbook review checks | >0 unresolved defects for release path | Weekly | Ops Lead | JTS-R-017 |
| KRI-018 | Privileged manual-step stability | `% privileged manual deployment steps executed without deviation` | Release rehearsal logs | <100% during pre-release rehearsals | Per release | Ops/Release Authority | JTS-R-018 |
| KRI-019 | PR governance compliance | `% PRs with risk/security/rollback evidence fields completed` | PR template audit | <100% for release-scoped PRs | Weekly | QA Lead + Security Lead | JTS-R-019 |
| KRI-020 | Terraform state policy consistency | `count(envs violating approved backend policy)` | Terraform provider config audit | >0 mismatches | Weekly | DevOps Lead | JTS-R-020 |
| KRI-021 | IAM wildcard scope | `count(wildcard action/resource statements)` | IAM policy review | Above approved exception budget | Biweekly | Security Lead | JTS-R-021 |
| KRI-022 | Network control documentation completeness | `% required network control fields populated and validated` | Security architecture review | <100% for release scope | Weekly | Security Lead | JTS-R-022 |
| KRI-023 | Recovery control completeness | `% critical data stores with validated failover/recovery procedures` | Recovery control review | <100% coverage | Weekly/Monthly | Ops/Release Authority | JTS-R-023 |
| KRI-024 | Fallback secret count | `count(fallback secret values in active release config)` | Config scan/review | >0 in release config | Daily (pre-release) | Security Lead | JTS-R-024 |
| KRI-025 | Mutable image tag count | `count(deploy references using mutable tags)` | Config scan/review | >0 in release path | Weekly | DevOps Lead | JTS-R-025 |
| KRI-026 | Default credential count | `count(default credential values in runtime config)` | Config scan/review | >0 in release config | Daily (pre-release) | Security Lead | JTS-R-026 |
| KRI-027 | Exposure/lifecycle violations | `count(policy violations for public access/destructive lifecycle defaults)` | IaC policy checks | >0 violations without accepted exception | Weekly | Security + Infra Leads | JTS-R-027 |
| KRI-028 | Embedded key incidents | `count(static keys embedded in infra/runtime definitions)` | IaC/security review | >0 unresolved in release branch | Weekly | Security + Infra Leads | JTS-R-028 |
| KRI-029 | DVC reproducibility reliability | `% branches with successful DVC-backed build verification` | CI reproducibility checks | <100% for data-changing branches | Weekly | Data Governance + DevOps Leads | JTS-R-029 |
| KRI-030 | Hotspot regression pressure | `count(top hotspot files above complexity/churn threshold)` | Complexity/churn reports | Upward trend over 2 review cycles | Monthly | App Engineering Lead | JTS-R-030 |
| KRI-031 | Forecast bias index | `median((actual - baseline_estimate) / baseline_estimate)` across completed release-significant work packages | Release planning and delivery variance records | >15% positive bias for 2 consecutive cycles or >30% in a single cycle | Biweekly/Monthly | Program/Delivery Authority + Risk Coordinator | JTS-R-031, JTS-R-033 |
| KRI-032 | Tail overrun incidence | `% release-significant work packages with overrun >50%` and `count(overrun >100%)` | Historical release performance records | >15% above 50% overrun or any >100% overrun without approved contingency | Monthly (and pre-release) | Program/Delivery Authority | JTS-R-032, JTS-R-033 |
| KRI-033 | Reference class forecast coverage | `% release-significant estimates with documented reference class and P80/P95 forecast` | Risk board packet and release planning artifacts | <100% for release scope | Weekly (pre-release) / Per release | Risk Coordinator + Program/Delivery Authority | JTS-R-031, JTS-R-032, JTS-R-033 |
