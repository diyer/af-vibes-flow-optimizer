# GUS Flow Queries

SOQL query templates for the flow optimizer. All queries use `sf data query --target-org gus --json` as the primary method.

All field names have been validated against GUS object descriptions.

**Important:** Run GUS queries sequentially, never in parallel.

---

## Setup Queries

### 1. team_resolution — Find EM's teams

Resolve teams where the current user is the Engineering Manager:
```sql
SELECT Id, Name, Engineering_Manager__r.Name, Engineering_Manager__r.Email,
  Scrum_Master__r.Name, Scrum_Master__r.Email, Product_Owner__r.Name,
  Active__c, Total_Members__c, Total_Dev__c, Total_QE__c,
  Say_Do_ratio__c, Velocity_Variation__c, Number_of_Open_Bugs__c,
  Average_Age_of_Bugs__c, Team_Work_In_Progress__c
FROM ADM_Scrum_Team__c
WHERE Name IN ('{TEAM_NAMES}')
AND Active__c = true
```

If team names are not provided, resolve by EM email:
```sql
SELECT Id, Name, Engineering_Manager__r.Name, Engineering_Manager__r.Email,
  Scrum_Master__r.Name, Total_Members__c, Total_Dev__c, Total_QE__c,
  Say_Do_ratio__c, Velocity_Variation__c, Number_of_Open_Bugs__c,
  Average_Age_of_Bugs__c, Team_Work_In_Progress__c
FROM ADM_Scrum_Team__c
WHERE Engineering_Manager__r.Email = '{USER_EMAIL}'
AND Active__c = true
```

### 2. team_members — Get team roster

```sql
SELECT Member_Name__r.Name, Member_Name__r.Email, Role__c, Allocation__c
FROM ADM_Scrum_Team_Member__c
WHERE Scrum_Team__r.Name = '{TEAM}'
AND Active__c = true
ORDER BY Role__c, Member_Name__r.Name
```

---

## Sprint-Scoped Queries

### 3. recent_sprints — Last 6 completed sprints with velocity

```sql
SELECT Name, Start_Date__c, End_Date__c,
  Completed_Story_Points__c, Committed_Points__c,
  Completed_Items__c, Committed_Items__c,
  Completion_Story_Points__c, Completion_Committed_Story_Points__c
FROM ADM_Sprint__c
WHERE Scrum_Team__r.Name = '{TEAM}'
AND End_Date__c <= TODAY
AND End_Date__c >= LAST_N_DAYS:180
ORDER BY End_Date__c DESC
LIMIT 6
```

**Interpretation:** Stable velocity = predictable flow. High variance = planning or scope problems. Compare `Committed_Points__c` to `Completed_Story_Points__c` for forecast accuracy (say-do ratio).

### 4. active_sprint_status — Current sprint items by status

```sql
SELECT Status__c, COUNT(Id) cnt, SUM(Story_Points__c) pts
FROM ADM_Work__c
WHERE Scrum_Team__r.Name = '{TEAM}'
AND Sprint__r.Start_Date__c <= TODAY
AND Sprint__r.End_Date__c >= TODAY
GROUP BY Status__c
```

**Interpretation:** Items piling up in a specific status = bottleneck at that SDLC stage. Use the status-to-stage mapping from `sdlc-reference-model.md`.

### 5. work_type_distribution — Bug/story/investigation mix

```sql
SELECT RecordType.Name, COUNT(Id) cnt, SUM(Story_Points__c) pts
FROM ADM_Work__c
WHERE Scrum_Team__r.Name = '{TEAM}'
AND Sprint__r.End_Date__c >= LAST_N_DAYS:90
AND Sprint__r.End_Date__c <= TODAY
GROUP BY RecordType.Name
```

**Interpretation:** Bug ratio above 30% suggests the team is in reactive mode. High Investigation ratio suggests unclear requirements or heavy tech debt.

### 6. stale_items — Open items not modified in 7+ days

```sql
SELECT Name, Subject__c, Status__c, RecordType.Name, Assignee__r.Name,
  Story_Points__c, LastModifiedDate, Days_In_Progress__c
FROM ADM_Work__c
WHERE Scrum_Team__r.Name = '{TEAM}'
AND Sprint__r.Start_Date__c <= TODAY
AND Sprint__r.End_Date__c >= TODAY
AND Status__c NOT IN ('Fixed', 'Closed', 'Duplicate', 'Never', 'Not a bug', 'Not Reproducible')
AND LastModifiedDate < LAST_N_DAYS:7
ORDER BY LastModifiedDate ASC
```

**Interpretation:** Stale items consume WIP slots without delivering value. They're flow killers.

### 7. carryover_items — Items open 30+ days still in active sprint

```sql
SELECT Name, Subject__c, Status__c, RecordType.Name, Assignee__r.Name,
  Story_Points__c, CreatedDate, Days_In_Progress__c, CycleTime__c
FROM ADM_Work__c
WHERE Scrum_Team__r.Name = '{TEAM}'
AND Sprint__r.Start_Date__c <= TODAY
AND Sprint__r.End_Date__c >= TODAY
AND Status__c NOT IN ('Fixed', 'Closed', 'Duplicate', 'Never', 'Not a bug', 'Not Reproducible')
AND CreatedDate < LAST_N_DAYS:30
ORDER BY CreatedDate ASC
```

**Interpretation:** Items open for 30+ days that are still in an active sprint were likely carried over. Chronic carryover signals estimation or scoping problems.

---

## Release/Build-Scoped Queries

### 8. active_build — Auto-detect current build

```sql
SELECT Name, Code_Line_Open__c, Feature_Freeze__c
FROM ADM_Build__c
WHERE Code_Line_Open__c <= TODAY
AND Feature_Freeze__c >= TODAY
AND (NOT Name LIKE '%.%')
AND (NOT Name LIKE '%-%')
ORDER BY Code_Line_Open__c DESC
LIMIT 1
```

Fallback if no active build (between freeze periods):
```sql
SELECT Name, Code_Line_Open__c, Feature_Freeze__c
FROM ADM_Build__c
WHERE Code_Line_Open__c > TODAY
AND (NOT Name LIKE '%.%')
AND (NOT Name LIKE '%-%')
ORDER BY Code_Line_Open__c ASC
LIMIT 1
```

### 9. epic_health — Epics by team and build

```sql
SELECT Id, Name, Health__c, Scheduled_Build__r.Name, Team__r.Name, Percent_Of_Work_Items_Complete__c
FROM ADM_Epic__c
WHERE Team__r.Name = '{TEAM}'
AND Scheduled_Build__r.Name = '{BUILD}'
ORDER BY Name
```

### 10. epic_completion — Work item breakdown per epic

```sql
SELECT Epic__c, Epic__r.Name, Status__c, COUNT(Id) cnt, SUM(Story_Points__c) pts
FROM ADM_Work__c
WHERE Epic__c IN ('{EPIC_ID_1}', '{EPIC_ID_2}')
GROUP BY Epic__c, Epic__r.Name, Status__c
ORDER BY Epic__r.Name, Status__c
```

**Important:** Do NOT filter by build or team — the epic IDs already ensure correctness.

### 11. release_bug_trend — Bugs created over the build window

```sql
SELECT Sprint__r.Name, COUNT(Id) cnt, SUM(Story_Points__c) pts
FROM ADM_Work__c
WHERE Scrum_Team__r.Name = '{TEAM}'
AND RecordType.Name = 'Bug'
AND CreatedDate >= {BUILD_START_DATE}
GROUP BY Sprint__r.Name
ORDER BY Sprint__r.Name
```

**Interpretation:** Rising bug creation sprint-over-sprint signals quality issues building up toward release.

### 12. release_readiness — Items in late-stage status for current build

```sql
SELECT Status__c, COUNT(Id) cnt, SUM(Story_Points__c) pts
FROM ADM_Work__c
WHERE Scrum_Team__r.Name = '{TEAM}'
AND Scheduled_Build__r.Name = '{BUILD}'
AND Status__c IN ('Integrate', 'Pending Release', 'Ready for Review', 'QA In Progress')
GROUP BY Status__c
```

---

## Supporting Queries (run on demand)

### 13. blocked_items — Items in Waiting status

```sql
SELECT Name, Subject__c, Status__c, RecordType.Name, Assignee__r.Name,
  LastModifiedDate, WaitTime__c, Sprint__r.Name
FROM ADM_Work__c
WHERE Scrum_Team__r.Name = '{TEAM}'
AND Status__c = 'Waiting'
AND LastModifiedDate >= LAST_N_DAYS:90
ORDER BY LastModifiedDate DESC
LIMIT 20
```

### 14. rework_indicators — Recent bugs that may indicate rework

```sql
SELECT Name, Subject__c, Priority__c, CreatedDate,
  Assignee__r.Name, Sprint__r.Name
FROM ADM_Work__c
WHERE RecordType.Name = 'Bug'
AND Scrum_Team__r.Name = '{TEAM}'
AND CreatedDate >= LAST_N_DAYS:60
ORDER BY Priority__c, CreatedDate DESC
LIMIT 30
```

### 15. review_queue — Items stuck in Ready for Review

```sql
SELECT Name, Subject__c, Assignee__r.Name, Story_Points__c,
  LastModifiedDate, Days_In_Progress__c
FROM ADM_Work__c
WHERE Scrum_Team__r.Name = '{TEAM}'
AND Status__c = 'Ready for Review'
AND Sprint__r.Start_Date__c <= TODAY
AND Sprint__r.End_Date__c >= TODAY
ORDER BY LastModifiedDate ASC
```

### 16. cycle_time_analysis — Completed items with real cycle time data

```sql
SELECT Name, Subject__c, RecordType.Name, Story_Points__c,
  CycleTime__c, LeadTime__c, WaitTime__c, InitiateTime__c,
  Days_In_Progress__c, Sprint__r.Name
FROM ADM_Work__c
WHERE Scrum_Team__r.Name = '{TEAM}'
AND Status__c IN ('Fixed', 'Closed')
AND Closed_On__c >= LAST_N_DAYS:90
ORDER BY Closed_On__c DESC
LIMIT 50
```

**Interpretation:** GUS has real cycle time fields — `CycleTime__c`, `LeadTime__c`, `WaitTime__c`, `InitiateTime__c`. These are not proxies. Use them to understand where time is actually spent.

---

## Notes

- Always use `--target-org gus --json` for all queries
- Replace placeholder values (`{TEAM}`, `{BUILD}`, etc.) with actual values at runtime
- Sprint filtering uses date-based approach (`Start_Date__c <= TODAY AND End_Date__c >= TODAY`) — ADM_Sprint__c has no Status field
- For teams that don't use sprints, fall back to date-range queries on `LastModifiedDate`
- GUS link format: `https://gus.lightning.force.com/lightning/r/ADM_Work__c/{Id}/view`
- Epic link format: `https://gus.lightning.force.com/lightning/r/ADM_Epic__c/{Id}/view`
