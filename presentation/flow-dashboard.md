# Flow Dashboard — Output Format

Template for presenting the team's flow data in Phase 2 (Current State Analysis). Presents GUS and Git data as **equal perspectives** — GUS shows what's being worked on and how it's tracked; Git shows how the code is actually moving.

---

## Format

```markdown
## Flow Dashboard — {TEAM_NAME}

### Team Profile
- **Members:** {N} ({N} Dev, {N} QE)
- **Scrum Lead:** {name}
- **Primary Repo(s):** {repo_url(s)}
- **Sprint Forecast Ratio (Say-Do):** {say_do_ratio}%
- **Open Bugs:** {N} (avg age: {N} days)
- **Current WIP:** {N} open stories

---

### Velocity & Delivery Trend (last 6 sprints)

| Sprint | Forecast | Completed | Completion % | Bug Ratio | Commits | Merges |
|--------|----------|-----------|-------------|-----------|---------|--------|
| {sprint} | {committed} pts | {completed} pts | {pct}% | {bug_pct}% | {n} | {n} |
| ... | ... | ... | ... | ... | ... | ... |

**GUS trend:** {velocity up/down/stable — interpretation}
**Git trend:** {commit/merge activity up/down/stable — interpretation}
**Combined read:** {e.g., "Velocity is stable but commit frequency is declining — work may be getting larger and less frequent."}

---

### Where Work Lives Right Now

#### GUS: Sprint Status Distribution

| Status | Items | Points | SDLC Stage |
|--------|-------|--------|-----------|
| New | {n} | {pts} | Requirements |
| In Progress | {n} | {pts} | Code Development |
| Ready for Review | {n} | {pts} | Code Review |
| QA In Progress | {n} | {pts} | Testing |
| Integrate | {n} | {pts} | Merge & QA |
| Waiting | {n} | {pts} | Blocked |
| Fixed | {n} | {pts} | Done (awaiting close) |

**GUS bottleneck:** {status with highest non-terminal count} → {SDLC stage}

#### Git: Code Activity Distribution

| Signal | Value | SDLC Stage |
|--------|-------|-----------|
| Active branches / in-flight work | {n} | Code Development |
| Commits last 2 weeks | {n} by {n} authors | Code Development |
| Merge frequency | {n}/week | Code Review → Merge |
| Merge concentration | {top merger}: {pct}% of merges | Code Review |
| Avg merge size | {n} files, {n} lines changed | Code Review |
| Hotfixes/reverts (last 30 days) | {n} | Maintain |
| Test commits as % of total | {pct}% | Testing |

**Git bottleneck:** {e.g., "Merges concentrated on one person" or "Large infrequent merges suggest long-lived branches"}

#### Combined Bottleneck Assessment

| SDLC Stage | GUS Signal | Git Signal | Confidence |
|------------|-----------|-----------|------------|
| {stage with signals from both} | {what GUS shows} | {what Git shows} | High (confirmed by both) |
| {stage with signal from one} | {what GUS shows} | No signal / No data | Medium (single source) |

**Primary bottleneck:** {stage} — {one-line explanation citing both data sources}

---

### Work Type Mix (last 3 sprints — GUS)

| Type | Items | Points | % of Total |
|------|-------|--------|-----------|
| User Story | {n} | {pts} | {pct}% |
| Bug | {n} | {pts} | {pct}% |
| Investigation | {n} | {pts} | {pct}% |
| Test Failure | {n} | {pts} | {pct}% |

#### Commit Type Mix (last 50 commits — Git)

| Type | Commits | % of Total |
|------|---------|-----------|
| Feature | {n} | {pct}% |
| Bug fix | {n} | {pct}% |
| Test | {n} | {pct}% |
| Refactoring | {n} | {pct}% |
| Merge | {n} | {pct}% |

**Combined read:** {e.g., "GUS shows 40% bugs but Git shows only 15% fix commits — bug items may be stalling rather than getting fixed."}

---

### Flow Timing

#### Cycle Time (GUS — completed items, last 90 days)

| Metric | Median | P90 |
|--------|--------|-----|
| Lead Time | {n} days | {n} days |
| Cycle Time | {n} days | {n} days |
| Wait Time | {n} days | {n} days |
| Days In Progress | {n} days | {n} days |

(Only show if CycleTime__c / LeadTime__c fields are populated)

#### Code Review Cycle (Git — from merge patterns)

| Metric | Value |
|--------|-------|
| Merges per week | {n} |
| Avg merge size (files changed) | {n} |
| Merge concentration (top reviewer) | {name}: {pct}% |
| Estimated branch lifespan | {short/medium/long based on merge size} |

**Combined read:** {e.g., "GUS shows 4-day average cycle time but Git shows merges only happen twice a week in large batches — code sits waiting for review."}

---

### Knowledge & Risk Distribution (Git)

| Author | Commits | % of Total | Primary Focus | Files Owned |
|--------|---------|-----------|---------------|------------|
| {name} | {n} | {pct}% | {feature/fix/test} | {top files} |
| ... | ... | ... | ... | ... |

**Top 5 file hotspots:**

| File | Commits | Unique Authors | Risk Level |
|------|---------|---------------|-----------|
| {path} | {n} | {n} | {High/Medium/Low} |

**Knowledge distribution:** {e.g., "Code ownership is concentrated — top 2 authors account for 70% of commits. Bus factor risk."}

---

### Stale Items (GUS — 7+ days untouched)

{count} items in current sprint haven't been updated in 7+ days:

| W# | Title | Status | Owner | Days Since Update |
|----|-------|--------|-------|------------------|
| {name} | {subject} | {status} | {assignee} | {days} |

(If none: "No stale items — good flow.")

---

### Release Health (Build {BUILD} — GUS)

| Metric | Value |
|--------|-------|
| Epics total | {n} |
| On Track / At Risk / Off Track | {n} / {n} / {n} |
| Items in Integrate/Pending Release | {n} ({pts} pts) |
| Bug trend (this build) | {rising/falling/stable} |

---

### Quality Signals (Git)

| Signal | Value | Trend |
|--------|-------|-------|
| Hotfixes in last 30 days | {n} | {up/down/stable} |
| Reverts in last 30 days | {n} | {up/down/stable} |
| Test-to-source commit ratio | {pct}% | {up/down/stable} |

**Combined with GUS:** {e.g., "Rising bug ratio in GUS (47%) aligns with increasing hotfix frequency in Git (5 in last 30 days) — quality issues are escaping to production."}

---

### Key Observations

1. **{Biggest finding}** — {cite both GUS and Git evidence where possible}
2. **{Second finding}** — {cite data source(s)}
3. **{Third finding}** — {cite data source(s)}

For each observation, note whether it's confirmed by both data sources (high confidence) or visible in only one (worth investigating further).
```

---

## Presentation Rules

- **GUS and Git are equal perspectives.** Present both and synthesize. Neither is supplemental to the other. GUS shows the ticket-level picture; Git shows the code-level picture. Together they tell the full story.
- **Always cross-reference.** When GUS shows a bottleneck (e.g., items in Ready for Review), check if Git confirms it (e.g., merge concentration on 1-2 people). When Git shows a pattern (e.g., low test commits), check if GUS confirms it (e.g., rising Test Failure items). Cross-referenced signals are high confidence.
- **Summary first, details on demand.** Show the key observations and combined bottleneck assessment before the detailed tables.
- **Map to SDLC stages.** Always connect both GUS statuses and Git signals to SDLC stages so the EM sees the workflow, not just raw data.
- **Only show populated sections.** If cycle time fields are empty, skip. If Git repos weren't found, skip Git sections but note the gap. Don't show empty tables.
- **Interpret, don't just display.** Every section should have a "Combined read" that synthesizes both sources into one insight.
