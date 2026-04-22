# Phase 2: Current State Analysis

## Trigger
Phase 1 complete — team resolved, sprint history pulled, workflow mapped.

> **References:** `data/gus-flow-queries.md` for GUS queries, `data/git-queries.md` for remote GitHub API queries, `data/sdlc-reference-model.md` for SDLC stage mapping, `presentation/flow-dashboard.md` for output format, `rules/conduct.md` for guardrails

## Purpose
Build a data-backed picture of the team's actual flow from **two equal perspectives** — GUS (ticket-level) and Git (code-level). Surface patterns the EM might not see. Present the combined flow dashboard and let the EM choose which metrics matter most.

---

## Step 1: Resolve repositories

Ask the EM (if not already known from Phase 1):
```
Which repositories does your team primarily work in?
(e.g., git.soma.salesforce.com/mobile/mobile-sdk)
```

Parse each URL into code host, org, and repo. Determine the API base URL and auth method per `data/git-queries.md`. Validate by checking recent commit authors against the GUS roster using Git query **G1**.

## Step 2: Run GUS sprint-scoped queries

Run these **sequentially** (GUS constraint):

1. **#4 active_sprint_status** — Current sprint items by status
2. **#5 work_type_distribution** — Bug/story/investigation mix (last 3 sprints)
3. **#6 stale_items** — Open items not modified in 7+ days
4. **#7 carryover_items** — Items open 30+ days still in active sprint

## Step 3: Run GUS release-scoped queries

1. **#8 active_build** — Auto-detect current build
2. **#9 epic_health** — Epics by team and build (if build found)
3. **#12 release_readiness** — Items in late-stage status

## Step 4: Run GUS cycle time query

1. **#16 cycle_time_analysis** — Completed items with CycleTime__c, LeadTime__c, WaitTime__c

If cycle time fields are not populated, note: "Cycle time fields aren't populated for this team. Using status distribution and Git merge patterns as the primary flow signals."

## Step 5: Run Git queries

Run these via the GitHub REST API against each repo (can run in parallel):

1. **G2 commit_velocity** — Commit frequency and volume (last 90 days)
2. **G3 commit_patterns** — Categorize commits by type (feature/fix/test/merge)
3. **G5 merge_activity** — Merge frequency, who merges, merge size
4. **G8 hotfix_frequency** — Reverts and hotfixes
5. **G4 author_contribution** — Per-developer commit distribution (cross-ref with GUS roster)

If time permits or the data warrants:
6. **G9 test_code_ratio** — Test file activity vs. source activity
7. **G10 file_hotspots** — Most frequently changed files

## Step 6: Cross-reference and synthesize

This is the critical step. Don't present GUS and Git as separate reports — synthesize them:

**For each SDLC stage, check both sources:**

| SDLC Stage | GUS Signal | Git Signal | Confidence |
|------------|-----------|-----------|------------|
| Requirements | Items in New/Triaged | (no Git signal expected) | GUS only |
| Code Development | Items in In Progress, Days_In_Progress | Commit frequency, author distribution | Both |
| Code Review | Items in Ready for Review | Merge concentration, merge frequency, branch lifespan | Both |
| Testing | Items in QA In Progress, Test Failures | Test commit ratio, test file activity | Both |
| Merge & QA | Items in Integrate | Merge size, revert frequency | Both |
| Release | Epic health, Pending Release items | Hotfix/cherry-pick patterns | Both |
| Maintain | Bug ratio, bug age, P0/P1 count | Hotfix frequency, revert rate | Both |

**Flag stages where both sources agree** — those are high-confidence bottlenecks.
**Flag stages where sources disagree** — those need the EM's interpretation.

## Step 7: Present the Flow Dashboard

Use the template from `presentation/flow-dashboard.md`. The dashboard presents:

1. **Team profile** (GUS + Git: members, repos, key metrics)
2. **Velocity & delivery trend** (GUS sprints + Git commit/merge activity side by side)
3. **Where work lives right now** (GUS status distribution + Git code activity, then combined bottleneck assessment)
4. **Work type mix** (GUS work types + Git commit types side by side)
5. **Flow timing** (GUS cycle time + Git review cycle side by side)
6. **Knowledge & risk distribution** (Git: author concentration, file hotspots)
7. **Stale items** (GUS)
8. **Release health** (GUS + Git quality signals)
9. **Key observations** (3 findings citing both data sources)

**Be opinionated in the observations.** Cross-reference both sources:
- "GUS shows 8 items stuck in Ready for Review AND Git shows merges concentrated on one person. Code review is confirmed as your bottleneck from both sides."
- "GUS bug ratio climbed to 47% AND Git shows 5 hotfixes in the last 30 days. Quality issues are escaping to production."
- "GUS velocity is stable but Git shows commit frequency declining and merge size increasing. Work is getting batched into larger chunks — this may cause problems next sprint."

## Step 8: Ask the EM (maximum 1 question)

```
Here's what your team's data shows — from both your work tracking and your code.

Does this match your experience, or is there something the data doesn't capture?
Which of these signals matters most to you for understanding your team's flow?
```

## Step 9: Handoff to the Prompt Playbook

After the EM has seen and reacted to the data:

```
Now that we can see how work is flowing through your team — from both the
ticket and the code perspective — it's time to dig into what this means
and what to do about it.

I'm going to give you a series of prompts to work through. These aren't
scripts — adjust them, push back on what I suggest, and make them your own.
The goal is to end up with a concrete improvement experiment you can take
back to your team.

Ready? Here's your first prompt:

"Looking at the data we just reviewed — both GUS and Git — where is work
getting stuck on my team? Which stage of the SDLC has the biggest
bottleneck, and what's the impact?"
```

## Exit Deliverable

- Complete flow dashboard presented with GUS and Git data as equal perspectives
- Combined bottleneck assessment citing both sources
- EM has validated or adjusted the picture
- EM has identified which metrics matter most to them
- Clear transition to the prompt playbook
