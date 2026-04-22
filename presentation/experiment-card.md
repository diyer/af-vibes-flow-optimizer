# Improvement Experiment — Output Template

This is the template the EM fills in through their prompt playbook conversation. The skill does not generate this — the EM produces it with Claude's help during Part 2.

---

## Template

```markdown
# Improvement Experiment

**Team:** {team_name}
**Date:** {date}
**EM:** {em_name}

---

## Background
Why are we doing this? What prompted us to look at our workflow?

{1-2 sentences — the context that led to this experiment}

---

## Current State
What does the data show about how work flows through our team?

| Metric | Value |
|--------|-------|
| {metric the EM chose} | {value} |
| {metric the EM chose} | {value} |
| {metric the EM chose} | {value} |

(Include only the metrics the EM identified as most important — not every metric from the dashboard)

---

## The Problem
What's the one bottleneck we're going to focus on?

{Single clear statement — where work gets stuck and what impact it has}

---

## Root Cause
Why does this bottleneck exist?

1. {Level 1 — the observable symptom}
2. {Level 2 — the structural cause}
3. {Level 3 — the systemic reason it persists}

---

## Future State
What does better look like?

| Goal | Current | Target | Timeline |
|------|---------|--------|----------|
| {goal} | {current value} | {target value} | {sprints/weeks} |

---

## What We'll Try
What specific changes are we going to make?

1. **{Action 1}** — {description with concrete first step}
2. **{Action 2}** — {description with concrete first step}

**Duration:** {how long we'll run this experiment — e.g., "2 sprints (4 weeks)"}

**First step this week:** {the most concrete immediate action}

---

## How We'll Know It's Working
What metrics will we track, and how often?

- **Metric:** {what to measure}
- **Check-in cadence:** {e.g., "review at each sprint retro"}
- **Reassess after:** {e.g., "3 sprints — re-run /flow-optimizer to compare"}

---

_Generated with /flow-optimizer on {date}. Re-run to compare progress._
```

---

## Usage Notes

- This template is a reference, not a rigid form. The EM's conversation with Claude should naturally produce these sections.
- The metrics table in Current State should only contain what the EM cares about — not a dump of everything.
- The Duration in "What We'll Try" is important — an experiment without a timeframe isn't an experiment.
- The "First step this week" forces concreteness. If the EM can't name a first step, the action isn't specific enough.
