# Feedback-Stabilized Software Development (FSSD)

## Definitions

- **Feedback latency**: Time between action and reliable signal about its impact; primary stability lever.
- **Signal quality**: Accuracy, timeliness, and relevance of project data used for decisions.
- **Perception noise**: Distortion between actual state and perceived state due to bias, lag, or poor instrumentation.
- **Loop strength**: Magnitude of effect one variable has on another in a feedback loop.
- **Schedule pressure**: Gap-driven urgency created by forecast date vs target date.
- **Rework load**: Effort spent correcting defects or redesigning prior work.
- **Learning rate**: Improvement in effective productivity per iteration from experience and feedback.
- **Workforce stability**: Degree to which team composition stays constant during delivery.
- **Onboarding drag**: Temporary productivity loss from integrating new members.
- **Control cadence**: Fixed rhythm for making tactical vs structural changes.
- **Bounded intervention**: Explicit cap on size/frequency of staffing, scope, or architecture changes per cycle.
- **Stable core**: Roadmap items with low volatility and high dependency impact.
- **Adaptive edge**: Roadmap items with high uncertainty, designed for rapid reprioritization.
- **Time-to-detect**: Elapsed time from defect introduction to detection.
- **Forecast calibration**: How well probability forecasts match observed outcomes over time.
- **Confidence score**: Team-rated certainty in estimate validity (not effort remaining).
- **Volatility score**: Expected likelihood a requirement changes materially.
- **Reversibility class**: Decision type: reversible, costly-to-reverse, irreversible.
- **Operating envelope**: Acceptable metric ranges before corrective action is mandatory.
- **Damping action**: Change that reduces oscillation (usually lower WIP, faster feedback, smaller batch size).

## Execution Playbook

### Step 1 - Initialize (Week 0-1)

- Define system boundary, success metrics, constraints, and decision rights.
- Baseline current latency, defect leakage, deploy frequency, forecast error, and team composition.

### Step 2 - Shape Inputs (Week 1-2)

- Run structured discovery with dual-channel evidence (stated needs + observed behavior).
- Produce living problem model with requirement clarity and volatility tags.

### Step 3 - Lock Structural Bets (Week 2-4)

- Conduct architecture sprint.
- Classify decisions by reversibility.
- Finalize stable core vs adaptive edge.
- Freeze team size target and skill mix plan.

### Step 4 - Establish Control Loops (Week 2-4)

- Set integration cycle (1-3 days), trunk-based workflow, mandatory observability per feature, layered test strategy, and dual-progress reporting.

### Step 5 - Run Weekly Tactical Control

- Review leading indicators.
- Apply bounded interventions only.
- Prefer WIP reduction, scope slicing, and defect-latency fixes before staffing changes.

### Step 6 - Run Monthly Structural Control

- Revisit architecture coupling, staffing policy, roadmap partitioning, and metric calibration.
- Permit only small structural changes per month.

### Step 7 - Close Production Loop

- Use progressive delivery, canaries, and rollback rehearsals.
- Treat production telemetry as planning input for the next cycle.

### Step 8 - Learn and Recalibrate

- Hold blameless postmortems.
- Update causal assumptions.
- Adjust thresholds based on observed loop behavior.

## Cadence and Owners

- **Daily**: Integration health, test failures, deployment blockers. Owner: engineering lead.
- **Weekly**: Forecast update, WIP compliance, defect latency, scope churn. Owners: PM + EM.
- **Biweekly**: Requirement clarity/volatility refresh. Owners: PM + design + tech lead.
- **Monthly**: Structural review and policy adjustment. Owners: leadership triad (product, engineering, architecture).

## Core Metrics and Guardrails

- **Feedback latency**: Target <= 3 days from code change to production signal.
- **Time-to-detect**: Target median <= 24 hours for high-severity defects.
- **WIP**: Hard cap by team capacity; breaches require explicit tradeoff decision.
- **Forecast calibration**: Rolling error band within +/-15% at 6-8 week horizon.
- **Scope churn**: <= 10% net change per month in stable core.
- **Team churn**: Avoid midstream net new hires unless risk threshold is breached.
- **Rework ratio**: Monitor rework effort as percent of total effort; rising trend triggers quality intervention.

## If-Then Intervention Rules

- If **forecast slip > 10%** for 2 consecutive weeks, reduce active initiatives before adding staff.
- If **time-to-detect** worsens 2 weeks in a row, pause new feature starts and increase test/instrumentation depth.
- If **scope churn** exceeds guardrail, shift items from stable core to adaptive edge and rebaseline forecast ranges.
- If **incident rate** rises after release, tighten rollout batch size and require rollback drill completion.
- If **onboarding drag** exceeds expected band, freeze hiring and reassign mentors to restore throughput.

## Minimum Artifact Set

- Living problem model with constraints and assumptions.
- Requirement register with clarity, volatility, and confidence scores.
- Reversibility decision log.
- Weekly control report: objective progress + confidence in remaining work.
- Risk register mapped to feedback loops.
- Postmortem log with policy updates.
