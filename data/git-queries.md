# Git Flow Queries — Remote GitHub API

Query templates for pulling code repository data via the GitHub REST API. Works against `github.com`, `git.soma.salesforce.com`, and `gitcore.soma.salesforce.com` — no cloning required.

These provide visibility into Code Development, Code Review, Merge, and Maintain stages of the SDLC — **equal in weight to GUS data** for understanding how work flows through the team.

**Git queries can run in parallel.** No sequential constraint.

---

## API Setup

All three code hosts use the GitHub API format. The only difference is the base URL and auth.

| Host | API Base | Auth |
|------|----------|------|
| `github.com` | `https://api.github.com` | Public repos need no auth. Private repos need a token via `Authorization: token {TOKEN}` |
| `git.soma.salesforce.com` | `https://git.soma.salesforce.com/api/v3` | Use git credential helper: extract token via `git credential fill` for host |
| `gitcore.soma.salesforce.com` | `https://gitcore.soma.salesforce.com/api/v3` | Same as git.soma |

### Helper: Get auth token for internal hosts

```bash
token=$(echo -e "protocol=https\nhost={CODE_HOST}\n" | git credential fill 2>/dev/null | grep '^password=' | cut -d= -f2-)
```

Then pass as: `curl -s -H "Authorization: token $token" "{API_BASE}/repos/{ORG}/{REPO}/..."` 

For `github.com` public repos, no auth header needed.

### Resolving the default branch

```bash
curl -s {AUTH_HEADER} "{API_BASE}/repos/{ORG}/{REPO}" | python3 -c "import sys,json; print(json.load(sys.stdin).get('default_branch','main'))"
```

---

## Setup Queries

### G1. discover_repos — Identify the team's repositories

**Step 1:** Ask the EM which repositories their team primarily works in.

```
Which repositories does your team work in?
(e.g., git.soma.salesforce.com/MobilePlatform/my-repo)
```

**Step 2:** Parse each URL into `{CODE_HOST}`, `{ORG}`, `{REPO}` and determine the API base.

**Step 3:** Validate by checking recent history for known team members (from GUS roster):

```bash
curl -s {AUTH_HEADER} "{API_BASE}/repos/{ORG}/{REPO}/commits?per_page=50&sha={BRANCH}" \
  | python3 -c "
import sys, json, collections
commits = json.load(sys.stdin)
authors = collections.Counter(c['commit']['author']['email'] for c in commits)
for email, count in authors.most_common():
    print(f'{count:4d}  {email}')
"
```

Scan commit authors against the team roster emails. If multiple team members appear, this is a team repo.

---

## Commit Activity Queries

### G2. commit_velocity — Commit frequency and volume (last 90 days)

**Important:** The GitHub API returns max 100 commits per page. You must paginate to get accurate counts — a single page will severely undercount active repos.

```bash
page=1
all_commits="[]"
while true; do
  batch=$(curl -s {AUTH_HEADER} "{API_BASE}/repos/{ORG}/{REPO}/commits?per_page=100&sha={BRANCH}&since={90_DAYS_AGO_ISO}&page=$page")
  count=$(echo "$batch" | python3 -c "import sys,json; print(len(json.load(sys.stdin)))" 2>/dev/null || echo 0)
  if [ "$count" -eq 0 ]; then break; fi
  all_commits=$(echo "$all_commits" "$batch" | python3 -c "
import sys, json
a = json.load(sys.stdin)
b = json.load(sys.stdin)
print(json.dumps(a + b))
")
  if [ "$count" -lt 100 ]; then break; fi
  page=$((page + 1))
done
echo "$all_commits" | python3 -c "
import sys, json
commits = json.load(sys.stdin)
print(f'Total commits: {len(commits)}')
if commits:
    print(f'Date range: {commits[-1][\"commit\"][\"author\"][\"date\"][:10]} to {commits[0][\"commit\"][\"author\"][\"date\"][:10]}')
    from collections import Counter
    by_week = Counter(c['commit']['author']['date'][:10] for c in commits)
    by_author = Counter(c['commit']['author']['email'] for c in commits)
    print(f'Unique authors: {len(by_author)}')
    print('\\nCommits per author:')
    for email, count in by_author.most_common():
        print(f'  {count:4d}  {email}')
    print(f'\\nCommits per day (last 14 days):')
    for date, count in sorted(by_week.items())[-14:]:
        print(f'  {date}: {count}')
"
```

**What to extract from results:**
- Total commits and date range covered (commits per week)
- Commits per author (map to team roster for team-only view)
- Day-of-week and time-of-day patterns
- Average commits per working day

**Interpretation:**
- Declining commit frequency = possible blockers, context switching, or work happening elsewhere
- Very uneven distribution across authors = knowledge silos or unbalanced workload
- Weekend/late-night commits = possible release pressure or timezone-driven patterns

### G3. commit_patterns — What type of work is being committed

```bash
curl -s {AUTH_HEADER} "{API_BASE}/repos/{ORG}/{REPO}/commits?per_page=100&sha={BRANCH}&since={90_DAYS_AGO_ISO}" \
  | python3 -c "
import sys, json, re
commits = json.load(sys.stdin)
categories = {'feature': 0, 'bugfix': 0, 'test': 0, 'refactor': 0, 'merge': 0, 'other': 0}
for c in commits:
    msg = c['commit']['message'].lower()
    first_line = msg.split('\n')[0]
    if re.search(r'^merge|merge pull|merge branch', first_line):
        categories['merge'] += 1
    elif re.search(r'fix|bug|patch|hotfix', first_line):
        categories['bugfix'] += 1
    elif re.search(r'test|spec|coverage', first_line):
        categories['test'] += 1
    elif re.search(r'refactor|clean|rename|move', first_line):
        categories['refactor'] += 1
    elif re.search(r'add|implement|feature|feat|new', first_line):
        categories['feature'] += 1
    else:
        categories['other'] += 1
total = len(commits)
print(f'Total commits: {total}')
for cat, count in sorted(categories.items(), key=lambda x: -x[1]):
    pct = (count/total*100) if total else 0
    print(f'  {cat:12s}: {count:4d} ({pct:.0f}%)')
"
```

**Interpretation:**
- High fix/hotfix ratio = quality issues escaping to main branch
- Low test commit ratio = possible testing gaps
- High merge commit frequency with few feature commits = integration bottleneck

### G4. author_contribution — Per-developer commit activity

Use the **fully paginated** data from G2 — group by author email and cross-reference with the GUS team roster. Do not re-fetch with a single page.

**Per-author focus area (last 20 commits by author):**

For drilling into a specific author's recent work, paginate here too:
```bash
page=1
author_commits="[]"
while true; do
  batch=$(curl -s {AUTH_HEADER} "{API_BASE}/repos/{ORG}/{REPO}/commits?per_page=100&sha={BRANCH}&since={90_DAYS_AGO_ISO}&author={AUTHOR_EMAIL}&page=$page")
  count=$(echo "$batch" | python3 -c "import sys,json; print(len(json.load(sys.stdin)))" 2>/dev/null || echo 0)
  if [ "$count" -eq 0 ]; then break; fi
  author_commits=$(echo "$author_commits" "$batch" | python3 -c "
import sys, json
a = json.load(sys.stdin)
b = json.load(sys.stdin)
print(json.dumps(a + b))
")
  if [ "$count" -lt 100 ]; then break; fi
  page=$((page + 1))
done
echo "$author_commits" | python3 -c "
import sys, json
commits = json.load(sys.stdin)
print(f'Total commits by author: {len(commits)}')
for c in commits[:20]:
    print(c['commit']['message'].split('\n')[0])
"
```

**Interpretation:**
- One author with 50%+ of commits = bus factor risk / knowledge concentration
- Team members in GUS roster with zero commits = may be blocked, doing non-code work, or in wrong repo
- Authors not in GUS roster but committing = possible cross-team dependencies

---

## Code Review Queries

### G5. merge_activity — PR merge patterns and review cycle

**Pull requests (last 30 merged):**
```bash
curl -s {AUTH_HEADER} "{API_BASE}/repos/{ORG}/{REPO}/pulls?state=closed&sort=updated&direction=desc&per_page=30&base={BRANCH}" \
  | python3 -c "
import sys, json
from datetime import datetime
prs = [p for p in json.load(sys.stdin) if p.get('merged_at')]
print(f'Merged PRs: {len(prs)}')
if prs:
    print(f'Date range: {prs[-1][\"merged_at\"][:10]} to {prs[0][\"merged_at\"][:10]}')
    from collections import Counter
    mergers = Counter(p['merged_by']['login'] if p.get('merged_by') else 'unknown' for p in prs)
    authors = Counter(p['user']['login'] for p in prs)
    print('\\nPR authors:')
    for user, count in authors.most_common():
        print(f'  {count:4d}  {user}')
    print('\\nMerged by:')
    for user, count in mergers.most_common():
        print(f'  {count:4d}  {user}')
    durations = []
    for p in prs:
        created = datetime.fromisoformat(p['created_at'].replace('Z','+00:00'))
        merged = datetime.fromisoformat(p['merged_at'].replace('Z','+00:00'))
        durations.append((merged - created).total_seconds() / 3600)
    durations.sort()
    median = durations[len(durations)//2]
    p90 = durations[int(len(durations)*0.9)]
    print(f'\\nPR open-to-merge: median {median:.1f}h, p90 {p90:.1f}h')
"
```

**Interpretation:**
- Low merge frequency with large merges = batching problem, long-lived branches
- Merges concentrated on 1-2 people = review/approval bottleneck (maps to GUS "Ready for Review" pileup)
- High merge frequency with small changes = good flow
- Long PR open-to-merge time = review bottleneck

### G6. branch_lifespan — Active unmerged branches

```bash
curl -s {AUTH_HEADER} "{API_BASE}/repos/{ORG}/{REPO}/branches?per_page=30" \
  | python3 -c "
import sys, json
branches = json.load(sys.stdin)
print(f'Total branches returned: {len(branches)}')
for b in branches:
    print(f'  {b[\"name\"]}')
"
```

**Open PRs (unmerged work in flight):**
```bash
curl -s {AUTH_HEADER} "{API_BASE}/repos/{ORG}/{REPO}/pulls?state=open&per_page=20" \
  | python3 -c "
import sys, json
from datetime import datetime, timezone
prs = json.load(sys.stdin)
print(f'Open PRs: {len(prs)}')
now = datetime.now(timezone.utc)
for p in prs:
    created = datetime.fromisoformat(p['created_at'].replace('Z','+00:00'))
    age_days = (now - created).days
    print(f'  {age_days:3d}d old — {p[\"user\"][\"login\"]:20s} — {p[\"title\"][:60]}')
"
```

**Interpretation:**
- PRs open longer than 1 week = long-lived branches, potential merge conflicts
- Many open PRs = review or integration bottleneck
- Correlate with GUS: long-lived PRs often correspond to items stuck in "In Progress" or "Ready for Review"

### G7. review_concentration — Who reviews and approves code

From G5 PR data — track who authored vs. who merged. If author and merger are consistently different people, identify the merger distribution.

**Interpretation:**
- 1-2 people doing all merges = review bottleneck (senior engineer overload)
- Authors merging their own code = possible lack of review process
- Well-distributed merges across 3+ team members = healthy review culture

---

## Quality Signal Queries

### G8. hotfix_frequency — Emergency and revert patterns

```bash
curl -s {AUTH_HEADER} "{API_BASE}/repos/{ORG}/{REPO}/commits?per_page=100&sha={BRANCH}&since={90_DAYS_AGO_ISO}" \
  | python3 -c "
import sys, json, re
commits = json.load(sys.stdin)
hotfixes = [c for c in commits if re.search(r'revert|hotfix|cherry.pick', c['commit']['message'], re.I)]
print(f'Hotfixes/reverts/cherry-picks: {len(hotfixes)} out of {len(commits)} commits')
for c in hotfixes:
    print(f'  {c[\"sha\"][:8]} {c[\"commit\"][\"author\"][\"date\"][:10]} — {c[\"commit\"][\"message\"].splitlines()[0][:80]}')
"
```

**Interpretation:**
- Frequent reverts = changes going out that shouldn't (review quality issue)
- Cherry-picks to release branches = late changes (release process issue)
- Hotfix branches = production issues (maps to GUS Bug/Maintain data)

### G9. test_code_ratio — Test file activity vs. source activity

```bash
curl -s {AUTH_HEADER} "{API_BASE}/repos/{ORG}/{REPO}/commits?per_page=30&sha={BRANCH}&since={90_DAYS_AGO_ISO}" \
  | python3 -c "
import sys, json
commits = json.load(sys.stdin)
shas = [c['sha'] for c in commits[:20]]
print(f'Sampling {len(shas)} commits for file-level data...')
" 
```

Then for each commit (sample 10-15 to avoid rate limits):
```bash
curl -s {AUTH_HEADER} "{API_BASE}/repos/{ORG}/{REPO}/commits/{SHA}" \
  | python3 -c "
import sys, json, re
data = json.load(sys.stdin)
files = data.get('files', [])
test_files = [f for f in files if re.search(r'test|spec|Test|Spec', f['filename'])]
src_files = [f for f in files if not re.search(r'test|spec|Test|Spec', f['filename'])]
print(f'Test files changed: {len(test_files)}, Source files changed: {len(src_files)}')
"
```

**Interpretation:**
- Test file changes < 30% of source changes = testing may not be keeping pace with development
- Test file changes close to or exceeding source changes = strong testing culture
- Correlate with GUS Test Failure items — high test failures + low test commits = systemic issue

### G10. file_hotspots — Frequently changed files (complexity/risk indicator)

Sample recent commits for file-level data (use same approach as G9):
```bash
curl -s {AUTH_HEADER} "{API_BASE}/repos/{ORG}/{REPO}/commits/{SHA}" \
  | python3 -c "
import sys, json
data = json.load(sys.stdin)
for f in data.get('files', []):
    print(f['filename'])
"
```

Aggregate across sampled commits to find:

| File | Commits | Authors | Risk |
|------|---------|---------|------|
| {path} | {n} | {n unique authors} | {high if many commits + few authors} |

**Interpretation:**
- Files with many commits by one author = knowledge concentration risk
- Files with many commits by many authors = possible merge conflicts, shared component
- Files that appear in both feature and fix commits = unstable area needing refactoring

---

## Cross-Referencing Git + GUS Data

The real power is correlating both sources:

| Git Signal | GUS Signal | Combined Insight |
|-----------|-----------|-----------------|
| Low commit frequency | Items stuck in "In Progress" | Developers may be blocked or context-switching |
| Merge concentration on 1-2 people | Items piling up in "Ready for Review" | Code review is a confirmed bottleneck — same signal from both sources |
| High hotfix/revert rate | Rising bug ratio | Quality issues escaping through review and testing |
| Uneven author distribution | One assignee with 5+ items | Workload imbalance confirmed at both code and ticket level |
| Low test commit ratio | Test Failure items increasing | Testing gaps confirmed at both code and ticket level |
| Large, infrequent merges | High carryover items | Work batching — items are large, take long, and carry over sprints |
| Long PR open-to-merge time | Items in Ready for Review | Review process is slow — confirmed at both code and ticket level |

---

## Notes

- **Ask the EM for repos first.** The EM knows where their team's code lives.
- **Map commit authors to GUS roster.** This connects code activity to people and makes both data sources reinforce each other.
- **Privacy:** Show team-level patterns and aggregates. Don't call out individual commit counts as performance metrics — frame as workload distribution and knowledge concentration.
- **Rate limits:** GitHub.com allows 60 unauthenticated requests/hour. Internal hosts are more generous but be mindful. Use `per_page=100` to minimize calls.
- **Auth for internal hosts:** Use `git credential fill` to extract tokens. This reuses the EM's existing git credentials — no extra setup.
- **Pagination:** The GitHub API returns max 100 items per page. For commit queries (G2, G4), you **must** paginate through all pages to get accurate counts. A single page will severely undercount active repos (e.g., reporting 5 commits when the real number is 78). Loop with `page=1,2,3...` until an empty response.
- **PR data is powerful.** The pulls endpoint gives open-to-merge time, author/reviewer distribution, and size — data that raw commit history can't provide.
