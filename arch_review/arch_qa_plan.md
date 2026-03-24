# 30/60/90 Plan (Architect + QA Folded)

## First 30 days — Orientation + Contracts + Security Baseline
- Map the end-to-end workflow (ingest -> Textract/PDF extraction -> NLP/PHI -> HIL UI -> SIMON) with a single golden-path walkthrough; capture all AWS resources, IAM roles, and required secrets.
- Build a contract catalog: enumerate all payloads and DynamoDB record shapes; pick a canonical schema source (likely form-validator) and define versioning/compatibility rules.
- Produce a PHI data-flow map and threat model; document retention/TTL, redaction boundaries, and KMS usage assumptions.
- Establish baseline SLOs (latency, success rate, backlog age, cost per document) and add initial alerts tied to those SLOs.
- Implement an end-to-end correlation ID standard (spec and propagation expectations).
- Stand up a golden data set (sample PDFs -> expected JSON) and a critical-path E2E test matrix (happy path + failures + retries).
- Identify operational risks (SELinux/nginx, secrets management, multi-runtime builds, LocalStack parity gaps).

## Days 31–60 — Stabilize + Enforce + Observability
- Enforce schema checks in CI/runtime; add contract tests across web <-> lambdas <-> data store.
- Standardize local testing recipes (Docker RIE + LocalStack); add a LocalStack-vs-AWS parity checklist and known gaps.
- Add idempotency keys/dedupe rules for critical Lambdas and define explicit state transitions in DynamoDB/SFN.
- Establish DLQ + replay/backfill tooling and runbooks; document failure handling.
- Improve deployment reproducibility and provenance (commit -> artifact -> deploy); verify Nix builds and Terraform/Terranix consistency.
- Add structured logging + correlation IDs across services; validate end-to-end traceability.
- Pull SELinux/nginx + HTTPS path forward into this window if risk is gating deployment.

## Days 61–90 — Scale + Resilience + Quality
- Expand E2E and failure-injection tests; run game days with replay/rollback drills.
- Add performance/concurrency tests (SFN bursts, DynamoDB hot partitions, S3 throughput).
- Introduce ML quality regression tests and drift detection using the labeled dataset.
- Implement infra test coverage: Terranix/Terraform plan validation + system-manager deploy smoke tests.
- Finalize SELinux/nginx/HTTPS deployment pattern and monitoring.

# Integrated Test & Risk Matrix (Initial)

## Test Matrix
- E2E Workflow: upload -> preprocessing -> Textract/PDF extraction -> NLP/PHI -> HIL review -> SIMON load
  - Coverage: happy path + partial failures + retries + duplicates
  - Priority: P0 (30–60 days)
- Contract Tests: JSON schema compatibility for inputs/outputs (web <-> lambdas <-> DynamoDB)
  - Coverage: versioning, backward compatibility, required fields
  - Priority: P0 (30–60 days)
- Idempotency/Replay: repeated events, retry storms, partial writes
  - Coverage: dedupe keys, state transitions, DLQ replay
  - Priority: P0 (60 days)
- LocalStack vs AWS Parity: S3/SFN/Textract behavior differences
  - Coverage: parity checklist + known gaps
  - Priority: P1 (30–60 days)
- Security/PHI Tests: redaction, access controls, auditability
  - Coverage: authZ boundaries, least privilege, PHI boundary tests
  - Priority: P0 (30–60 days)
- Performance/Load: SFN concurrency, DynamoDB hot partitions
  - Coverage: stress tests, throttle behavior
  - Priority: P1 (60–90 days)
- ML Regression/Drift: accuracy against labeled dataset
  - Coverage: regression thresholds, drift alerts
  - Priority: P1 (60–90 days)
- Infra Tests: Terraform/Terranix plan, system-manager deployment
  - Coverage: deploy smoke tests, rollback verification
  - Priority: P1 (60–90 days)

## Risk Matrix
- P0 - PHI/Compliance: unclear redaction/retention/KMS/audit trails
  - Mitigation: PHI data-flow map + threat model; explicit retention + access review; PHI tests.
- P0 - Idempotency/Replay: duplicate events corrupt downstream state
  - Mitigation: idempotency keys, explicit state transitions, DLQ + replay tooling.
- P1 - Observability: distributed workflow lacks traceability
  - Mitigation: structured logs + correlation IDs; add alerting tied to SLOs.
- P1 - Contract Drift: multi-runtime services with implicit schemas
  - Mitigation: contract catalog + schema registry; CI compatibility gates.
- P1 - Deployment Complexity: Nix + Terranix + wrappers create rollback risk
  - Mitigation: artifact provenance, staged rollouts, rollback drills.
- P1 - LocalStack Parity: false confidence in local tests
  - Mitigation: parity checklist + AWS smoke tests.
- P2 - SELinux/nginx: platform bottleneck for HTTPS and reverse proxy
  - Mitigation: move remediation to 60-day window; test SELinux policy changes.

# Milestone Checklist (Owners + Acceptance Criteria)

## Milestone 1 (Day 0–30): Orientation + Contracts + Security Baseline
- Workflow map + resource inventory — Owner: Architecture Lead
  - Acceptance: Single golden-path diagram and written narrative; all AWS resources/IAM roles/secrets enumerated; dependencies between lambdas, SFN, DynamoDB, S3 documented.
- Contract catalog + schema authority — Owner: Architecture Lead + App Lead
  - Acceptance: Catalog lists every payload and DynamoDB record shape; canonical schema source selected; versioning + compatibility policy written; schemas published in repo.
- PHI data-flow + threat model — Owner: Security/Compliance
  - Acceptance: PHI boundary diagram, retention/TTL policy, KMS assumptions, and access review cadence documented; risks logged with mitigation owners.
- Baseline SLOs + alerts — Owner: Platform/Infra
  - Acceptance: SLO targets defined (latency, success rate, backlog age, cost/document); alert thresholds configured and validated with test signals.
- Correlation ID standard — Owner: Platform/Infra + App Lead
  - Acceptance: Spec documented; at least one end-to-end flow proves correlation ID propagation across web + lambdas + DynamoDB logs.
- Golden data set + E2E test matrix — Owner: QA Lead
  - Acceptance: Curated sample PDFs + expected JSON outputs; test matrix covering happy path + failure + retry scenarios.
- Risk register initialized — Owner: Architecture Lead
  - Acceptance: Risk list with priority, owner, mitigation, and review cadence.

## Milestone 2 (Day 31–60): Stabilize + Enforce + Observability
- Schema checks in CI/runtime — Owner: App Lead + QA Lead
  - Acceptance: CI gates on schema compatibility; runtime validation for inbound/outbound payloads in critical services.
- Local testing standardization — Owner: QA Lead
  - Acceptance: One documented recipe for Docker RIE + LocalStack; parity checklist with known AWS gaps.
- Idempotency + explicit state transitions — Owner: Architecture Lead
  - Acceptance: Idempotency keys defined for critical lambdas; DynamoDB/SFN states documented and enforced.
- DLQ + replay/backfill tooling — Owner: Platform/Infra
  - Acceptance: DLQ configured; replay/backfill script or process documented; runbook validated with a test replay.
- Deployment provenance — Owner: Platform/Infra
  - Acceptance: Traceable chain from commit -> artifact -> deploy; Nix + Terranix build verification in CI.
- Structured logs + traceability — Owner: Platform/Infra
  - Acceptance: Logs include correlation ID + event metadata; sample trace shows cross-service linkage.
- SELinux/nginx + HTTPS path advanced — Owner: Platform/Infra
  - Acceptance: SELinux policy decision documented; HTTPS plan validated in staging or prototype.

## Milestone 3 (Day 61–90): Scale + Resilience + Quality
- Failure-injection + game days — Owner: QA Lead + Platform/Infra
  - Acceptance: At least one game day executed; rollback/replay runbooks validated; issues tracked.
- Performance/load testing — Owner: QA Lead
  - Acceptance: Concurrency + hot partition tests executed; baseline metrics captured; SLO impact assessed.
- ML regression + drift detection — Owner: Data/ML Lead
  - Acceptance: Labeled dataset in CI; regression thresholds defined; drift triggers documented.
- Infra tests (Terra/Terranix + system-manager) — Owner: Platform/Infra
  - Acceptance: Plan validation and deploy smoke tests executed; failures create actionable alerts.
- Finalize HTTPS deployment pattern — Owner: Platform/Infra
  - Acceptance: HTTPS running in target environment with monitoring; SELinux/nginx resolved or approved workaround.
