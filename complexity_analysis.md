# Complexity and Churn Analysis

Date: 2026-03-20

## Executive Summary

This repository is a medium-to-large polyglot monorepo with complexity concentrated in operationally critical areas.

- Total footprint (`scc.txt`): 460 files, 110,690 lines, 91,635 code lines, 4,278 total complexity.
- Complexity concentration: Python contributes 2,172 complexity (50.8%), TypeScript contributes 1,489 (34.8%).
- Change pressure (`churn.txt`): weighted churn 5,435 across 1,708 files.
- Churn concentration: `packages/*` contributes 82.8% of churn; `packages/lambdas` (34.7%) and `packages/hil-web-service` (27.3%) dominate.

Bottom line: complexity reduction should prioritize high-churn, high-control-flow files in `packages/hil-web-service` and `packages/lambdas`.

## Data Sources and Method

- Source 1: `churn.txt` generated from:
  - `git log --name-only --pretty=format: | sort | uniq -c | sort -n`
- Source 2: `scc.txt` language/code/complexity summary.
- Derived metrics:
  - Weighted churn totals and concentration by directory.
  - Complexity density (`complexity / kLoC`) and complexity per file.
  - Hotspot burden approximation: `churn_count x current_LOC` for prioritized code files.

Notes:

- Churn counts file touches, not line-level deltas.
- SCC complexity is language-level aggregate unless per-file SCC output is also captured.
- Approximate burden is used for prioritization, not absolute risk scoring.

## Git Churn Findings

### Churn distribution

- Parsed files: 1,708
- Weighted churn total: 5,435

| Churn bin | Files | Share |
|---|---:|---:|
| 1 | 706 | 41.3% |
| 2-3 | 650 | 38.1% |
| 4-10 | 267 | 15.6% |
| 11-20 | 59 | 3.5% |
| 21+ | 26 | 1.5% |

Interpretation: most files are low-touch; a small tail of files absorbs ongoing change pressure.

### Churn concentration

Top-level weighted churn:

| Area | Weighted churn | Share |
|---|---:|---:|
| packages | 4,500 | 82.8% |
| modules | 196 | 3.6% |
| docs | 127 | 2.3% |
| infrastructure | 115 | 2.1% |
| data | 90 | 1.7% |

Second-level weighted churn:

| Area | Weighted churn | Share |
|---|---:|---:|
| packages/lambdas | 1,887 | 34.7% |
| packages/hil-web-service | 1,482 | 27.3% |
| packages/scripts | 335 | 6.2% |
| packages/jts-frontend | 215 | 4.0% |
| packages/jts-deploy | 200 | 3.7% |

### Highest-churn files

| Churn | File |
|---:|---|
| 67 | `packages/hil-web-service/src/app/page.tsx` |
| 62 | `packages/hil-web-service/src/app/api/managers/dynamo_manager.ts` |
| 60 | `packages/hil-web-service/src/app/forms/[id]/page.tsx` |
| 48 | `packages/hil-web-service/src/app/api/managers/formManager.ts` |
| 35 | `modules/systems/hil-web-service-host-dev/default.nix` |
| 34 | `modules/nixos/services/hil-web-service/default.nix` |
| 33 | `.github/workflows/main.yml` |
| 33 | `packages/hil-web-service/src/components/ui/FormDataView/FormDataView.tsx` |
| 30 | `packages/lambdas/process-textract/default.nix` |
| 30 | `packages/scripts/pdf-to-json/pdf-to-json.py` |

## SCC Findings

### Repo totals

- Files: 460
- Lines: 110,690
- Code lines: 91,635
- Complexity: 4,278

### Complexity by language

| Language | Files | Code | Complexity | Complexity share | Complexity/kLoC | Complexity/file |
|---|---:|---:|---:|---:|---:|---:|
| Python | 88 | 19,798 | 2,172 | 50.8% | 109.7 | 24.7 |
| TypeScript | 139 | 32,564 | 1,489 | 34.8% | 45.7 | 10.7 |
| Nix | 81 | 6,293 | 385 | 9.0% | 61.2 | 4.8 |
| Terraform | 18 | 1,645 | 132 | 3.1% | 80.2 | 7.3 |
| Shell | 7 | 714 | 68 | 1.6% | 95.2 | 9.7 |

Interpretation:

- Python has the highest decision density and highest complexity per file.
- TypeScript has the largest code volume and substantial complexity concentration in high-churn UI/API modules.

## Combined Hotspots (Churn x Current LOC)

Approximate production-code burden (`churn x LOC`) highlights where refactoring likely yields the highest operational return.

| Burden | Churn | LOC | File |
|---:|---:|---:|---|
| 59,630 | 67 | 890 | `packages/hil-web-service/src/app/page.tsx` |
| 56,296 | 62 | 908 | `packages/hil-web-service/src/app/api/managers/dynamo_manager.ts` |
| 36,180 | 60 | 603 | `packages/hil-web-service/src/app/forms/[id]/page.tsx` |
| 29,430 | 27 | 1,090 | `packages/lambdas/process-textract/process_textract_lambda.py` |
| 24,240 | 48 | 505 | `packages/hil-web-service/src/app/api/managers/formManager.ts` |
| 14,586 | 33 | 442 | `packages/hil-web-service/src/components/ui/FormDataView/FormDataView.tsx` |
| 13,380 | 30 | 446 | `packages/scripts/pdf-to-json/pdf-to-json.py` |
| 10,244 | 26 | 394 | `packages/hil-web-service/src/utils/formDataUtility.tsx` |
| 6,496 | 29 | 224 | `packages/hil-web-service/src/app/api/forms/[id]/save/route.ts` |
| 4,725 | 25 | 189 | `packages/lambdas/pdf4700-to-json/pdf_to_json.py` |

## Why This Is Important

- Reliability: these hotspots include lock management, form save/approve/reject flows, audit logging, and OCR/ML extraction paths.
- Delivery velocity: high-churn high-complexity files create merge friction, slower reviews, and higher regression probability.
- Operability: complex side-effect orchestration (DynamoDB/S3/Step Functions) increases failure mode ambiguity and incident triage time.
- Maintainability: contributor onboarding and safe change throughput decline as control flow and coupling increase.

## Proposed Complexity Reduction Plan

## Phase 0: Baseline and guardrails (1 week)

- Add complexity guardrails for changed files first.
  - TypeScript: ESLint `complexity`, `max-depth`, `max-lines-per-function`.
  - Python: Ruff/Pylint complexity checks (`C901`, branch/statement limits).
- Capture current baseline metrics and freeze key behavior contracts before refactor.
- Require regression suite pass before complexity-driven merges.

Deliverables:

- Baseline report committed to repo.
- CI gate for touched files only.

## Phase 1: TypeScript API and manager decomposition (2 weeks)

Target files:

- `packages/hil-web-service/src/app/api/managers/dynamo_manager.ts`
- `packages/hil-web-service/src/app/api/managers/formManager.ts`
- `packages/hil-web-service/src/app/api/forms/[id]/save/route.ts`

Actions:

- Split read/filter/sort/pagination from lock lifecycle and write/update expression logic.
- Move shared validation-error traversal/counting into reusable utilities.
- Use explicit command-style functions for save/approve/reject orchestration.

Success criteria:

- 25-35% complexity reduction in target files.
- No contract changes in route responses.

## Phase 2: UI container simplification (2 weeks)

Target files:

- `packages/hil-web-service/src/app/page.tsx`
- `packages/hil-web-service/src/app/forms/[id]/page.tsx`
- `packages/hil-web-service/src/components/ui/FormDataView/FormDataView.tsx`

Actions:

- Separate data orchestration hooks from presentational components.
- Extract table configuration, row action logic, and filter query builders into pure helpers.
- Reduce cross-cutting state updates in single components.

Success criteria:

- 30% reduction in component-level control flow complexity.
- No UX regression in filtering, pagination, lock badges, and detail navigation.

## Phase 3: Python extraction pipeline decomposition (2-3 weeks)

Target files:

- `packages/lambdas/process-textract/process_textract_lambda.py`
- `packages/lambdas/pdf4700-to-json/pdf_to_json.py`
- `packages/scripts/pdf-to-json/pdf-to-json.py`

Actions:

- Split module into pure transform stages + I/O adapters.
- Isolate AWS side effects (DynamoDB/S3) from field extraction logic.
- Convert large conditional blocks to strategy/dispatch maps by form type/page.

Success criteria:

- 30-40% complexity reduction in `process_textract_lambda.py`.
- Stable output contracts for canonical fixture sets.

## Phase 4: Ratchet and sustain (ongoing)

- Raise complexity gates from changed-files to repo-wide thresholds over time.
- Add architectural tests to prevent new god modules.
- Track monthly trend: complexity totals, hotspot count, and escaped defect rate.

## Execution Risk Assessment

Refactoring this codebase carries meaningful execution risk due to system criticality and side-effect-heavy flows.

### Why this is risky

1. Behavioral coupling risk

- Core workflows combine lock checks, validation, rollups, audit updates, and persistence in single paths.
- Small sequencing changes can alter business behavior.

2. Side-effect ordering risk

- Approval/rejection and extraction flows coordinate DynamoDB, S3, and Step Functions.
- Reordering retries/reverts can leave partial state or stale lock behavior.

3. Concurrency and lock semantics risk

- Lock acquisition/release/expiry logic is conditional and time-based.
- Refactor can introduce subtle race regressions.

4. Nondeterminism risk in OCR/ML paths

- Some checks already acknowledge model-output variability.
- Exact-output regressions can be hard to detect without invariants.

5. Coverage and pipeline gap risk

- PR CI runs `nix flake check --show-trace`, but E2E coverage is not always exercised on every PR path.
- Some check scripts are intentionally permissive to avoid hard failures in certain environments.

### Risk register

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Lock lifecycle regression | Medium | High | Add lock state transition tests; canary monitoring for lock conflicts |
| Save/approve/reject behavior drift | Medium | High | Contract tests + dual-path comparison for refactored handlers |
| OCR extraction variance misclassified as regression | High | Medium | Move from exact diffs to invariant + threshold-based checks |
| Hidden side-effect ordering bug | Medium | High | Isolate side effects behind adapters; test retry/rollback paths explicitly |
| CI false confidence | Medium | High | Add required changed-file test gates and targeted E2E smoke run for hotspots |

## Regression Stability Strategy

Goal: refactor safely while preserving runtime behavior and confidence.

### Current baseline test assets

- HIL web service tests: 39 files under `packages/hil-web-service/src/**/*.test.{ts,tsx}`.
- Lambda Python tests: 13 files under `packages/lambdas/**/tests/**/*.py`.
- Nix checks: 4 checks under `checks/**/default.nix`.

### Stability approach

1. Behavior freeze before refactor

- Snapshot API contracts and representative payloads for targeted modules.
- Record lock-state transitions and audit log mutation expectations.

2. Dual-path execution for high-risk refactors

- Run old and new implementations against the same fixture sets.
- Compare outputs by strict equality where deterministic; invariants where nondeterministic.

3. Incremental rollout by blast radius

- Refactor one hotspot at a time.
- Merge only when unchanged behavior is demonstrated by contracts and tests.

4. Gate on changed-files first

- Block merge on failing tests/lint/complexity checks for touched files.
- Ratchet thresholds downward as confidence improves.

5. Canary and rollback

- Monitor save failures, lock conflicts, extraction errors, and approval/rejection failures.
- Define explicit rollback trigger thresholds before rollout.

### Recommended verification commands

Primary CI parity:

- `nix flake check --show-trace`

Service-level tests:

- `npm --prefix packages/hil-web-service test`

Targeted lambda suites:

- `python -m pytest packages/lambdas/process-textract/tests/test_ocr4700-to-json.py`
- `python -m pytest packages/lambdas/pdf4700-to-json/tests/test_pdf-to-json.py`

Deterministic processing checks:

- `nix build .#checks.x86_64-linux.da4700_sup`
- `nix build .#checks.x86_64-linux.dd1380`

Optional E2E smoke (when available):

- `nix run .#packages.x86_64-linux.end-to-end-tests.da4700 -- up --tui=false --no-server`

## Quantitative Targets

- Reduce total complexity from 4,278 to <= 3,400 (about 20%).
- Reduce Python complexity density from 109.7 to <= 80 complexity/kLoC.
- Reduce TypeScript complexity density from 45.7 to <= 38 complexity/kLoC.
- Reduce top 10 hotspot complexity by >= 30% each.
- Keep changed-file regressions at 0 in critical save/approve/reject/lock and OCR extraction flows.

## Recommended Next Actions

1. Approve Phase 0 and add changed-file complexity/test gates.
2. Execute Phase 1 on `dynamo_manager.ts` and `formManager.ts` first.
3. Add dual-path contract harness for `process_textract_lambda.py` before decomposition.
4. Review metrics after each phase and adjust thresholds only after stable cycles.
