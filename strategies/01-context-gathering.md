# Phase 1: Context Gathering

## Trigger
User invokes `/flow-optimizer` or says "help me analyze my team's workflow."

> **References:** `data/gus-flow-queries.md` for SOQL patterns, `data/sdlc-reference-model.md` for SDLC stages, `rules/conduct.md` for guardrails

## Purpose
Establish who the EM is, what team(s) they manage, and how their workflow generally operates. Get data in front of them fast.

---

## Step 1: Resolve the team

If the EM provides team names, use query **#1 (team_resolution)** with those names.

If not, ask:
```
What scrum team(s) do you manage? (e.g., Mobile SDK, Mobile Core Services)
```

Run the query to get team details: size, EM, Scrum Lead, say-do ratio, open bugs, WIP.

## Step 2: Get the roster

Run query **#2 (team_members)** for each team to get members and roles.

## Step 3: Get recent sprint history

Run query **#3 (recent_sprints)** for each team to get the last 6 sprints with velocity data.

## Step 4: Present what you found

Share a brief summary:
```
Your team: {Team Name} — {N} members ({N} Dev, {N} QE)
Scrum Lead: {name}
Last 6 sprints: velocity ranging from {min} to {max} pts, averaging {avg}.
Sprint forecast ratio: {say_do}%
```

## Step 5: Map the workflow and gather context

Ask the EM (maximum 4 questions total in this phase):

**Question 1 (required):**
```
Walk me through how a typical story moves from idea to production on your team.
What does the journey look like?
```

Map their description to the 8 SDLC stages from `data/sdlc-reference-model.md`. If their description is vague, propose a mapping and confirm:
```
Based on typical Salesforce engineering workflows, I'd map your flow as:
Requirements → Spike & Design → Code Development → Testing & Debugging →
Code Review & Pre-checkin → Merge & QA → Release → Maintain

Does that match how your team works, or would you adjust anything?
```

**Question 2 (required):**
```
How does your team keep GUS work items in sync with code changes?
For example: update GUS status as you go, batch update at sprint end,
@mention work items in PRs, or something else?
```

Record the answer — it determines how to interpret GUS status data in Phase 2:
- **Real-time sync:** GUS status is a reliable signal for where work stands.
- **Batch/async sync (@mentions, sprint-end updates):** GUS status will lag behind actual code progress. Rely more heavily on Git signals for development and review stages, and do not draw conclusions about "GUS hygiene" or "stale work" based on GUS status alone.

**Question 3 (required):**
```
Which repos does your team actively commit to? Please include everything —
primary app repos, infrastructure, helm charts, config, tooling, CLI tools,
not just the main codebase.
```

Record all repos — every one will be queried in Phase 2 for a complete picture of the team's code activity.

**Question 4 (only if relevant):**
```
Where is your team currently using AI tools (Claude Code, Copilot, etc.) in this workflow?
```

If the EM doesn't know or says "nowhere," that's fine — note it and move on.

## Exit Deliverable

Before moving to Phase 2, you should have:
- Team name(s), size, and roster
- Last 6 sprints with velocity data
- The EM's description of their workflow mapped to SDLC stages
- GUS sync practice (real-time, batch, @mentions, etc.)
- Complete list of active repositories
- Any current AI tool usage noted

Transition to Phase 2:
```
Good — I have the context I need. Now let me pull your team's flow data
and show you what the numbers say.
```
