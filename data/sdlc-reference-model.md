# SDLC Reference Model

Eight stages that reflect how Salesforce engineering teams actually work. Used to map GUS data and Git signals to the software development lifecycle.

---

## Stages

### 1. Requirements
Story writing, acceptance criteria, backlog grooming, estimation.

**GUS signals:**
- Work items in `New` or `Triaged` status
- User Story record type creation rate
- Story point distribution (large stories 8+ pts may indicate unclear requirements)

**Git signals:** None typically — this stage is pre-code.

---

### 2. Spike & Design
Investigations, technical design, architecture decisions.

**GUS signals:**
- `Investigation` record type items
- Investigation-to-story ratio (high ratio suggests unclear requirements or heavy tech debt)
- Time investigations spend in `In Progress`

**Git signals:** Spike branches, design doc commits.

---

### 3. Code Development
Feature work, bug fixes, implementation.

**GUS signals:**
- Work items in `In Progress` status
- `Days_In_Progress__c` field on ADM_Work__c
- `First_Time_In_Progress__c` timestamp
- WIP count per assignee

**Git signals:**
- Commit frequency by author
- Branch activity
- Lines changed per commit

---

### 4. Testing & Debugging
Unit tests, manual testing, test failures, debugging.

**GUS signals:**
- Work items in `QA In Progress` status
- `Test Failure` record type items
- Test Failure count (`of_Test_Failures__c`)
- `Resolution__c` field for test failure outcomes

**Git signals:**
- Test file commit frequency
- Test-to-source ratio in commits

---

### 5. Code Review & Pre-checkin
PR review, feedback loops, approval.

**GUS signals:**
- Work items in `Ready for Review` status
- Time spent in `Ready for Review` (derived from `LastModifiedDate` changes)
- Items accumulating in this status = review bottleneck

**Git signals:**
- PR open-to-merge time (via codesearch history)
- Number of reviewers per PR
- Review comment density

---

### 6. Merge & QA
Integration, QA sign-off, merge conflicts.

**GUS signals:**
- Work items in `Integrate` status
- Time in `Integrate`

**Git signals:**
- Merge frequency
- Merge conflict indicators (reverted commits, force pushes)

---

### 7. Release
Build integration, release readiness, deployment.

**GUS signals:**
- Work items in `Pending Release` status
- Epic health (`Health__c` on ADM_Epic__c)
- Epic completion percentage
- Items scheduled for current build (`Scheduled_Build__c`)
- Build dates (`Code_Line_Open__c`, `Feature_Freeze__c` on ADM_Build__c)

**Git signals:**
- Release branch activity
- Cherry-pick frequency (late changes)

---

### 8. Maintain
Production bugs, incidents, operational health, customer escalations.

**GUS signals:**
- Bug record type creation rate
- Bug-to-story ratio
- P0/P1 bug count and age
- `Gack_Occurrences__c`, `Occurrences_Past_30_Days__c`
- Bug `Priority__c` distribution
- `Age__c`, `Age_With_Scrum_Team__c` on bugs

**Git signals:**
- Hotfix branch frequency
- Production branch commit patterns

---

## GUS Status → SDLC Stage Mapping

| GUS Status | SDLC Stage |
|------------|-----------|
| New | Requirements |
| Triaged | Requirements |
| In Progress | Code Development |
| Investigating | Spike & Design |
| Ready for Review | Code Review & Pre-checkin |
| QA In Progress | Testing & Debugging |
| Integrate | Merge & QA |
| Pending Release | Release |
| Fixed | Release (code done, awaiting QA sign-off) |
| Closed | Complete |
| Waiting | Blocked (any stage) |

## Key Metrics Available from GUS

These fields exist on ADM_Work__c and provide real flow data (not proxies):

| Field | Label | What It Measures |
|-------|-------|-----------------|
| `CycleTime__c` | Cycle Time | Time from start to completion |
| `LeadTime__c` | Lead Time | Time from creation to completion |
| `WaitTime__c` | Wait Time | Time spent waiting/blocked |
| `InitiateTime__c` | Initiate Time | Time from creation to first action |
| `Days_In_Progress__c` | Days In Progress | Days in active development |
| `First_Time_In_Progress__c` | First Time In Progress | When work actually started |
| `Closed_On__c` | Closed On | When item was closed |
| `Resolved_On__c` | Resolved On | When item was resolved |
| `Age__c` | Age | Total age of the item |
| `Age_With_Scrum_Team__c` | Age With Scrum Team | Age since assigned to team |

## Key Metrics Available from Sprint Object

| Field | Label | What It Measures |
|-------|-------|-----------------|
| `Completed_Story_Points__c` | Velocity | Points completed in the sprint |
| `Committed_Points__c` | Forecast Story Points | Points committed at sprint start |
| `Committed_Items__c` | Forecast Items | Items committed at sprint start |
| `Completed_Items__c` | Completed Items | Items completed in the sprint |
| `Completion_Story_Points__c` | Completion Story Points | Completion percentage by points |
| `Completion_Committed_Story_Points__c` | Completion Forecast Story Points | Forecast accuracy by points |

## Key Metrics Available from Team Object

| Field | Label | What It Measures |
|-------|-------|-----------------|
| `Say_Do_ratio__c` | Sprint Forecast Ratio | Forecast accuracy over time |
| `Velocity_Variation__c` | Velocity Variation | Stability of delivery |
| `Throughput_Variation__c` | Throughput Variation | Stability of item completion |
| `Number_of_Open_Bugs__c` | Number of Open Bugs | Current bug load |
| `Team_Work_In_Progress__c` | WIP | Open stories in progress |
| `Average_Age_of_Bugs__c` | Average Age of Bugs | Bug resolution speed |
| `Engineering_Manager__c` | Engineering Manager | EM lookup for team resolution |
