# Complexity Analysis - Executive Summary

Date: 2026-03-20

## Snapshot

- Repository size: 460 files, 91,635 code lines, 4,278 total complexity (`scc.txt`).
- Complexity concentration:
  - Python: 2,172 complexity (50.8%), 19,798 code lines.
  - TypeScript: 1,489 complexity (34.8%), 32,564 code lines.
- Change pressure: weighted churn 5,435 across 1,708 files (`churn.txt`).
- Churn concentration:
  - `packages/*`: 82.8%
  - `packages/lambdas`: 34.7%
  - `packages/hil-web-service`: 27.3%

## Primary Hotspots (Highest ROI Targets)

These files combine high change frequency with high control-flow density:

- `packages/hil-web-service/src/app/page.tsx` (churn 67, LOC 890)
- `packages/hil-web-service/src/app/api/managers/dynamo_manager.ts` (churn 62, LOC 908)
- `packages/hil-web-service/src/app/forms/[id]/page.tsx` (churn 60, LOC 603)
- `packages/hil-web-service/src/app/api/managers/formManager.ts` (churn 48, LOC 505)
- `packages/lambdas/process-textract/process_textract_lambda.py` (churn 27, LOC 1,090)
- `packages/scripts/pdf-to-json/pdf-to-json.py` (churn 30, LOC 446)

## Why This Matters

- These hotspots sit on core workflows: lock lifecycle, save/approve/reject paths, audit updates, OCR/ML extraction, and DynamoDB/S3 updates.
- High complexity in high-churn files increases regression probability, slows review cycles, and raises incident triage time.
- Reducing complexity in these areas improves reliability and delivery speed at the same time.

## Execution Risk (Why Refactor Is Risky)

- Behavioral coupling: business logic and side effects are interleaved in large functions.
- Side-effect ordering: DynamoDB/S3/Step Functions sequencing changes can produce partial-state bugs.
- Concurrency sensitivity: lock acquisition/release/expiry logic is race-prone.
- OCR/ML nondeterminism: strict output equality is not always stable.
- Coverage gaps: PR CI centers on `nix flake check`; high-value E2E paths are not always in every PR run.

Risk level: Medium-High unless rollout is staged with contract checks and canary monitoring.

## How We Keep Regression Testing Stable

1. Freeze behavior before refactor:
   - Snapshot route contracts, lock transitions, and audit-log expectations.
2. Use dual-path validation for risky modules:
   - Compare old/new outputs on the same fixtures.
   - Use strict diffs for deterministic paths, invariant checks for ML/OCR paths.
3. Gate by changed files first:
   - Require tests + complexity checks only for touched modules.
4. Canary and rollback:
   - Monitor save failure rate, lock conflict rate, extraction error rate, and approval/rejection failures.
   - Define rollback thresholds before deployment.

## 6-Week Plan (Condensed)

- Week 1: Baseline and guardrails
  - Add complexity gates for changed files (TS + Python).
  - Establish contract baselines for hotspot routes and processors.
- Weeks 2-3: Refactor TS managers
  - Decompose `dynamo_manager.ts` and `formManager.ts` into smaller command/services.
- Weeks 3-4: Refactor TS page containers
  - Split orchestration from presentation in `app/page.tsx` and `forms/[id]/page.tsx`.
- Weeks 4-6: Refactor Python extraction pipeline
  - Decompose `process_textract_lambda.py` into pure transforms + I/O adapters.
- Ongoing: ratchet thresholds and monitor trendline.

## Success Targets

- Total complexity: 4,278 -> <= 3,400 (about 20% reduction).
- Python density: 109.7 -> <= 80 complexity/kLoC.
- TypeScript density: 45.7 -> <= 38 complexity/kLoC.
- Top 10 hotspot files: >= 30% complexity reduction each.
- No regression increase in critical save/approve/reject/lock and OCR extraction flows.

## Immediate Recommendation

Approve Phase 0 and start with `dynamo_manager.ts` + `formManager.ts` first; this gives the best early risk-reduction-to-effort ratio while preserving delivery cadence.
