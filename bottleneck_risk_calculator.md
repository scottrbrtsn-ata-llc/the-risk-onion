# Bottleneck Risk Calculator (Expanded v1)

## Purpose

Estimate planning and delivery risk when team size, backlog size, parallel lanes, PR volume, PR size, review capacity, and work overlap interact.

This is designed to catch structural bottlenecks early, especially in mixed human + agent execution models.

## Inputs

- `N`: total executors (humans plus weighted agents)
- `T`: ready backlog task count
- `L`: independent parallel lanes
- `B`: blocker rate (`0.0` to `1.0`)
- `PR`: active PR count in the planning window
- `K`: average KLOC per PR
- `R`: active reviewers
- `V`: reviewer throughput (KLOC per reviewer per day)
- `O`: overlap index (`0.0` to `1.0`), representing task collision likelihood across PRs

### Defaults (if unknown)

- `B = 0.15`
- `K = 0.40`
- `V = 0.80`
- `O = 0.35`

## Agent Weighting

Use weighted executor count:

- `N_eff = N_humans + alpha * N_agents`
- `alpha` range: `0.3` to `0.6` (default `0.4`)

Use `N = N_eff` in the formulas below.

Rationale: agents can produce work quickly, but integration and acceptance remain constrained by human review and merge capacity.

## Core Concurrency

- `C = max(1, min(T, L))`  (true concurrency ceiling)
- `P = N / C`  (staffing pressure ratio)
- `I = max(0, 1 - C / N)`  (forced-idle floor)

### Execution Risk

- `ExecRisk = clamp(0.6*I + 0.4*clamp((P - 1)/1.5, 0, 1), 0, 1)`

Interpretation:

- If `N > C`, waiting is structural.
- If `P > 1.4`, queueing and handoff overhead become dominant.

## Review Bottleneck (PR Count + PR Size)

Reviewer bottlenecks are modeled directly from PR count and KLOC per PR.

- `ReviewLoad = PR * K`
- `ReviewCap = max(0.1, R * V)`
- `ReviewPressure = ReviewLoad / ReviewCap`
- `ReviewRisk = clamp((ReviewPressure - 1)/1.5, 0, 1)`

Interpretation:

- Higher `PR` increases queue depth.
- Higher `K` increases cycle time per review and raises defect escape risk.
- `ReviewPressure > 1.0` means demand exceeds reviewer capacity.

## Task Collision Risk

Collision risk grows when many PRs compete in too few lanes and touch overlapping code.

- `PRPerLane = PR / max(1, L)`
- `CollisionRisk = clamp(O * max(0, PRPerLane - 1)/2, 0, 1)`

Interpretation:

- If `PRPerLane` rises above `1`, collision and rework risk rise rapidly.

## Merge Conflict Drag

Merge risk increases as overlap and PR size increase.

- `K_ref = 0.40`
- `SizePressure = max(0, (K - K_ref)/K_ref)`
- `MergeRisk = clamp(O * PRPerLane * min(1, SizePressure)/3, 0, 1)`

Interpretation:

- Large PRs in overlapping lanes amplify rebase churn, conflict retries, and integration delay.

## Blocked Work Amplification

- `BlockRisk = clamp(B * (1 + 1/max(1, L)), 0, 1)`

Interpretation:

- Low `L` makes each blocked lane more damaging to throughput.

## Total Score

Compute a final risk score from `0` to `100`:

- `Score = 100 * (0.30*ExecRisk + 0.25*ReviewRisk + 0.20*CollisionRisk + 0.15*MergeRisk + 0.10*BlockRisk)`

## Risk Bands

- `0-24`: Low
- `25-44`: Guarded
- `45-64`: Medium
- `65-79`: High
- `80-100`: Critical

## Hard Warning Flags

- `N > min(T, L)` -> structural waiting risk
- `PR / L > 1.2` -> collision and queueing risk
- `K > 0.6` KLOC/PR -> review and merge drag risk
- `ReviewPressure > 1.0` -> reviewer bottleneck is active
- `ReviewPressure > 1.5` -> critical reviewer bottleneck

## Why This Matters (Especially With Agents)

- More executors do not increase throughput after the concurrency ceiling is reached.
- Agents can increase output velocity faster than review/integration capacity can absorb.
- Excess PR volume and oversized PRs move the bottleneck to reviewers and merges.
- Low lane count magnifies blocker propagation and reduces system resilience.
- Planning without these constraints leads to optimistic schedules and late-stage delivery failure.

## Common Failure Modes When Ignored

- Concurrent work overload: too many active branches for available review lanes.
- Task collision: overlapping edits in shared files create duplication and rework.
- Reviewer bottleneck: PR queue growth delays feedback, merge, and release decisions.
- Merge conflict drag: repeated rebases and conflict resolution consume execution time.
- Blocked-work cascades: waiting on one dependency stalls downstream tasks.
- Quality regression: rushed reviews and integration shortcuts increase defect escapes.
- Security/compliance exposure: high-risk changes are under-reviewed or accepted without evidence.
- Governance breakdown: unclear ownership and stale acceptance decisions weaken go/no-go discipline.

## Risks Threatened If Not Managed

- Delivery risk: missed milestones and unstable forecast accuracy.
- Reliability risk: increased incident probability after release.
- Security and compliance risk: delayed remediation, weak control evidence, and audit gaps.
- Operational risk: repeated hotfixes, rollback events, and degraded release confidence.
- Team sustainability risk: reviewer overload, context switching, and burnout.

## Practical Usage Notes

1. Recalculate weekly and before each release-go/no-go checkpoint.
2. Track trendline, not just snapshot score.
3. If score is `High` or `Critical`, reduce WIP and increase lane independence before adding more executors.
4. Treat reviewer throughput as a first-class capacity metric.
5. Keep PR size small to reduce merge drag and improve review quality.
