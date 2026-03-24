# Initial Plan: Step Functions + Lambda Log Aggregation (AWS)

## Decision Matrix (Top)

| Option | What It Is | Delivery Speed | Implementation Effort | Run Cost | Ops UX / MTTR Impact | Compliance / Governance Complexity | Recommendation |
|---|---|---|---|---|---|---|---|
| A. CloudWatch Unified Ops Workspace | Standardized logging + saved CloudWatch queries/dashboards as single ops console | Fast (days to 2 weeks) | Low | Low-Medium | Medium-High improvement quickly | Low-Medium | **Do now (Phase 1)** |
| B. CloudWatch -> Firehose -> S3 -> Athena | Central AWS-native log lake with CloudWatch for live triage | Medium (2-6 weeks) | Medium | Low-Medium at scale | High for historical analysis and audit investigations | Medium | **Do next (Phase 2)** |
| C. CloudWatch -> Firehose -> OpenSearch (+ S3 archive) | Indexed search/timeline UI for deep incident response | Medium-Slow (4-10 weeks) | Medium-High | Medium-High | Very high for incident triage speed | Medium-High | **Conditional (Phase 3, SLA-triggered)** |
| D. External SIEM (Splunk/Datadog/Equivalent) | Third-party observability platform for correlation and alerting | Slow (depends on procurement) | High | High | Very high if fully adopted | High | Optional only if pre-approved enterprise standard |

## BLUF / TLDR (Top)

- Recommended path is **A -> B**, with **C** only if measured MTTR remains above target after Phase 2.
- Immediate fix is to make CloudWatch truly usable as one operator workspace (schema standard, correlation IDs, saved queries, dashboards, and alarms).
- Within 30 days, add durable centralized aggregation (Firehose -> S3 -> Athena) for compliance, long retention, and investigation depth.
- Keep OpenSearch as a data-driven uplift, not a default commitment, due to cost and ownership overhead.
- Success criteria: >=99% correlated traces, alert fidelity for injected failures, and time-to-visibility SLAs for both CloudWatch and Athena.

## Comparative Analysis: This Plan vs ELK and Prometheus/Grafana

| Approach | What It Optimizes For | End-to-End Log Tracing (SFN + Lambda) | Metrics / SLO Dashboards | Ops Overhead | Cost Predictability | Recommendation for JTS |
|---|---|---|---|---|---|---|
| This plan (`A -> B`, optional `C`) | Fast operational improvement with AWS-native durability and governance | High (CloudWatch now, Athena/OpenSearch later) | Medium-High (CloudWatch native) | Low-Medium | High | Primary path |
| ELK stack (self-managed Elasticsearch/Logstash/Kibana) | Deep indexed search and custom investigation workflows | Very High | Medium | High | Medium-Low | Consider only if AWS-native indexed option cannot meet MTTR |
| Prometheus + Grafana (metrics-focused) | Service health, SLOs, and alerting from metrics/time-series | Low by itself (not a log pipeline) | Very High | Medium | Medium | Use as a complement, not a replacement, for logs |
| Prometheus/Grafana + Loki | Unified metrics + logs in Grafana | Medium-High | Very High | Medium-High | Medium | Viable alternative if team standardizes on Grafana ecosystem |

### Practical Interpretation

- ELK most closely maps to this plan's optional indexed-search phase (similar role to OpenSearch), not to the full phased strategy.
- Prometheus/Grafana is best for metrics and SLO visibility; it does not replace centralized workflow log aggregation unless paired with a log backend.
- For current JTS needs (Step Functions + Lambda traceability in AWS), this plan remains the lowest-risk path to a single ops view with clear compliance controls.

### When to Reconsider Platform Choice

- Reconsider ELK/OpenSearch-class indexing if post-Phase-2 MTTR and incident-trace latency still miss target SLA.
- Reconsider Grafana-first strategy if organization-wide observability standards require shared dashboards across many non-AWS systems.
- Keep S3/Athena archival regardless of index/UI choice to preserve durable, low-cost audit and forensic capability.

## Problem Statement

CloudWatch logs for Step Functions and Lambda are currently hard to use as a single operational view. We need one place where ops can follow an end-to-end execution path across the entire workflow stack.

## Current Observations (Repo-Based)

- Step Functions create one CloudWatch log group per workflow and log at `ALL` with execution data included.
- Step Functions log retention is set to 1 day.
- Lambda log groups are not explicitly managed in Terraform today (default per-function behavior).
- State machines already carry execution context, which can be used as a cross-service correlation key.
- Existing plans already call for structured logging and correlation IDs.

## Goals

- Provide a single operational experience for tracing one form execution across Step Functions and Lambda.
- Reduce mean time to detect and mean time to resolve incidents.
- Preserve compliance and auditability (PHI-aware handling, encryption, access controls).
- Keep implementation practical within current Terraform-managed AWS infrastructure.

## Non-Goals (Initial Phase)

- Full replacement of CloudWatch as the immediate operator console.
- Large-scale re-architecture of processing workflows.
- Broad expansion into unrelated observability domains (APM, full tracing, etc.) before logging baseline is stable.

## Aggregation Options

### Option A - CloudWatch Unified Ops Workspace (Fastest)

Use CloudWatch as the single-pane operator UI by standardizing log schema, correlation fields, saved Logs Insights queries, and dashboards across all workflows/functions.

Pros:
- Fastest path to usable single place.
- Lowest platform risk and smallest change surface.
- Fully AWS-native and simple to adopt.

Cons:
- Limited long-horizon analytics compared with a central log lake.
- Cross-account or advanced historical investigations are less ergonomic.

Best for:
- Immediate ops relief in days, not months.

### Option B - CloudWatch -> Firehose -> S3 -> Athena (Recommended Backbone)

Stream CloudWatch logs into encrypted S3 via Firehose, then query with Athena while keeping CloudWatch for live triage.

Pros:
- Strong retention, cost control, and audit trail.
- Scales better for historical analysis and reporting.
- Works well with GovCloud-style constraints and existing AWS tooling.

Cons:
- Slightly slower query UX than indexed search tools.
- Requires ingestion health monitoring and schema discipline.

Best for:
- Durable, compliance-friendly aggregation with low lock-in.

### Option C - CloudWatch -> Firehose -> OpenSearch (+ S3 Archive)

Send logs to OpenSearch for fast correlation and investigation, with S3 as long-term archive.

Pros:
- Best operator search and timeline experience.
- Strong incident triage and exploratory analysis.

Cons:
- Highest operational overhead and cost.
- Requires index lifecycle tuning and stronger platform ownership.

Best for:
- Teams with high incident volume and strict MTTR targets that justify index costs.

### Option D - External SIEM (Splunk/Datadog/Equivalent)

Forward logs to an external observability platform for enterprise dashboards, alerting, and correlation.

Pros:
- Richest feature set and mature workflows.
- Potentially unifies multiple systems beyond this stack.

Cons:
- Highest compliance/procurement/integration complexity.
- Potential data egress and boundary considerations.

Best for:
- Organizations already standardized on an approved external platform.

## Recommendation

Adopt a phased `Option A -> Option B` strategy, with `Option C` as a conditional uplift if MTTR remains high.

Why this path:
- Delivers immediate operator value quickly.
- Preserves AWS-native control and compliance posture.
- Builds a durable data foundation before investing in higher-cost indexing.

## Target Architecture Boundaries

1. Producers
   - Step Functions and all Lambdas emit structured JSON logs.
   - Required correlation fields are included on success, retry, and failure paths.

2. Collection
   - CloudWatch log groups for Step Functions and Lambda are explicitly managed and tagged.
   - Retention and encryption policy are standardized by environment.

3. Transport
   - Subscription filters and Firehose route logs to central storage.
   - Delivery failures are captured and alerted.

4. Storage
   - CloudWatch for hot operational window.
   - S3 for durable archive and long-term retention.
   - Optional OpenSearch for short-lived indexed search.

5. Query and Ops UX
   - CloudWatch dashboard + saved queries for on-call operations.
   - Athena (and optionally OpenSearch) for investigations and reporting.

6. Governance
   - Schema, access controls, encryption, retention, and audit standards.

## Phased Rollout Plan

### Quick Win (0-2 Weeks)

- Increase Step Functions log retention from 1 day to an operational minimum (example: 14-30 days).
- Define and publish logging schema + correlation contract.
- Create one CloudWatch dashboard for all key workflows and lambdas.
- Create saved Logs Insights queries for end-to-end trace by correlation id/execution id.
- Add baseline alarms for Step Functions failures/timeouts and Lambda errors/throttles.

### 30-Day Plan

- Bring Lambda log groups under Terraform management.
- Implement CloudWatch subscription pipeline to Firehose -> S3.
- Add Athena tables/views for execution-centric investigations.
- Add ingestion health alarms (freshness, delivery failure, missing partition/data).
- Add reconciliation checks between source log volume and sink volume.

### 60-90 Day Plan

- Add OpenSearch only if incident response remains too slow.
- Tune lifecycle tiers (hot/warm/cold) and retention by log class.
- Add schema drift detection and CI checks for log contract changes.
- Expand to cross-account aggregation if needed.

## Risks and Mitigations

### Cost Risk

Risk:
- `ALL` Step Functions logging with execution payloads can be expensive at scale.

Mitigations:
- Tune logging level where practical.
- Reduce payload verbosity and redact non-essential fields.
- Use S3 lifecycle policies and budget alarms.

### Compliance and PHI Risk

Risk:
- Execution data and Lambda payloads may include sensitive fields.

Mitigations:
- Enforce redaction/masking before log emit where possible.
- Restrict access via least-privilege roles.
- Encrypt at rest with KMS and audit access paths.

### Operability Risk

Risk:
- Schema drift, missing correlation IDs, and pipeline gaps reduce trust in logs.

Mitigations:
- Required schema fields and validation checks.
- Alert on missing correlation fields and ingestion failures.
- Daily canary execution with trace verification.

## Minimum Governance Standards

### Required Log Fields

- `timestamp`
- `level`
- `environment`
- `service`
- `workflow_name`
- `state_machine_arn`
- `lambda_function`
- `execution_arn`
- `correlation_id`
- `request_id`
- `form_id` (when available)
- `status`
- `error_type` / `error_code` (when available)
- `data_classification`

### Retention Baseline

- CloudWatch hot window: 14-30 days.
- S3 archive: policy-defined long-term retention (example: 1-7 years by data class).
- Optional OpenSearch: short hot window (example: 7-30 days) with rollover.

### Access and Security

- Least-privilege IAM for read/query roles.
- Separate PHI-restricted access roles.
- KMS-backed encryption for CloudWatch, Firehose, S3, and index stores.
- CloudTrail auditing for log access and policy changes.

## QA and Validation Plan (Initial)

### Functional Checks

- Validate happy path, retry path, timeout path, and failure path for each major workflow family.
- Confirm one correlation id can reconstruct full path across Step Functions + all invoked Lambdas.
- Validate dashboards and saved queries show expected events for canary runs.

### Non-Functional Checks

- Visibility SLA: CloudWatch p95 <= 2 minutes.
- Lake availability SLA (if enabled): Athena visibility p95 <= 15 minutes.
- Completeness target: source vs sink count delta <= 1% per hourly window.

### Pass/Fail Criteria

- Pass when >= 99% executions have complete correlated traces.
- Fail when missing correlation fields exceed 0.5% of events.
- Fail when expected alerts do not fire for injected failure scenarios.

### Daily Canary (Ops)

- Trigger one synthetic execution per critical workflow using a known canary correlation id.
- Verify end-to-end trace appears in CloudWatch within SLA.
- If lake enabled, verify same canary appears in Athena within SLA.

## Initial Decisions Needed

- Final retention targets by environment and data classification.
- Whether OpenSearch is required now or deferred behind SLA evidence.
- Ownership model for observability pipeline operations (platform vs app team).
- Final minimum schema and enforcement mechanism.

## Immediate Next Action

Start Phase 1 with CloudWatch unification and correlation standardization, then gate Phase 2 rollout on measured improvements in traceability and incident response speed.

## Implementation Task Backlog (Bottom)

### Phase 1 - CloudWatch Single-Place Ops (0-2 Weeks)

- **OBS-001**: Define and publish structured logging contract.
  - Owner: Platform + App Leads
  - Acceptance: Required fields documented and adopted by all critical workflow lambdas.
- **OBS-002**: Standardize correlation id propagation from Step Functions to all lambdas.
  - Owner: App Lead
  - Acceptance: End-to-end trace works from one id across success, retry, and failure paths.
- **OBS-003**: Raise Step Functions log retention from 1 day to operational baseline.
  - Owner: Platform/Infra
  - Acceptance: Retention policy verified in deployed environments.
- **OBS-004**: Build CloudWatch dashboard + saved Logs Insights query pack.
  - Owner: Platform/Infra + Ops
  - Acceptance: Ops can investigate a full execution from a single dashboard/query set.
- **OBS-005**: Add baseline alarms (SFN failures/timeouts, Lambda errors/throttles).
  - Owner: Platform/Infra
  - Acceptance: Alert tests pass for synthetic failure and timeout scenarios.
- **OBS-006**: Establish daily observability canary workflow run.
  - Owner: QA + Ops
  - Acceptance: Daily report confirms traceability and alert-path health.

### Phase 2 - Centralized Aggregation Backbone (30 Days)

- **OBS-101**: Manage Lambda log groups explicitly in Terraform.
  - Owner: Platform/Infra
  - Acceptance: Lambda log groups are tagged, encrypted, and retention-controlled by IaC.
- **OBS-102**: Implement CloudWatch subscription pipeline to Firehose -> S3.
  - Owner: Platform/Infra
  - Acceptance: End-to-end ingestion is healthy with no sustained delivery failures.
- **OBS-103**: Create Athena external tables/views for execution-centric queries.
  - Owner: Data/Platform
  - Acceptance: Investigators can query by correlation id, workflow, status, and error type.
- **OBS-104**: Add ingestion freshness and delivery failure alerts.
  - Owner: Platform/Infra
  - Acceptance: Alerts trigger within SLA when pipeline is stalled or failing.
- **OBS-105**: Add source-vs-sink reconciliation checks.
  - Owner: QA + Platform
  - Acceptance: Hourly mismatch threshold <=1% with alerting on breach.
- **OBS-106**: Implement governance controls for access and data class handling.
  - Owner: Security/Compliance + Platform
  - Acceptance: Role model, KMS usage, and audit logging validated.

### Phase 3 - Indexed Search Uplift (60-90 Days, Conditional)

- **OBS-201**: Evaluate OpenSearch need using post-Phase-2 MTTR and incident metrics.
  - Owner: Architecture + Ops
  - Acceptance: Written go/no-go decision with measured evidence.
- **OBS-202**: If approved, deploy Firehose/OpenSearch ingestion and index templates.
  - Owner: Platform/Infra
  - Acceptance: Search and timeline views support full execution reconstruction.
- **OBS-203**: Configure index lifecycle management and cost guardrails.
  - Owner: Platform/Infra + FinOps
  - Acceptance: Hot/warm/cold policy active and monthly spend within target band.
- **OBS-204**: Add OpenSearch ingestion and query health alerts.
  - Owner: Platform/Infra
  - Acceptance: Pipeline degradation or index failures alert within SLA.

### Cross-Phase Governance and Verification Tasks

- **OBS-301**: Publish and version schema contract change policy.
  - Acceptance: Schema changes require review and backward-compatibility notes.
- **OBS-302**: Add CI checks for required log fields in critical paths.
  - Acceptance: CI fails when required structured fields are missing.
- **OBS-303**: Add runbook for incident triage using the single ops workspace.
  - Acceptance: On-call can follow documented steps without tribal knowledge.
- **OBS-304**: Quarterly retention and access review.
  - Acceptance: Evidence of review and remediation actions recorded.
