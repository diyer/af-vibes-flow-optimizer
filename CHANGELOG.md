# Changelog

## v0.2.0 — 2026-04-29

### Fixed
- **Git commit counts now paginate all results.** Previously capped at 100 commits (single API page), which severely undercounted active contributors. G2 (commit_velocity) and G4 (author_contribution) now loop through all pages.

### Added
- **Upfront EM questions for context.** Phase 1 now asks:
  - How the team keeps GUS in sync with code (real-time, batch, @mentions) — prevents false "GUS hygiene" conclusions
  - All active repos including infra, helm, config, tooling — ensures complete contributor picture
- **Sprint-age-aware interpretation.** Dashboard now calculates days into sprint and calibrates expectations: Day 1-3 with 73% "New" items is normal, not alarming.
- **Conditional GUS/Git correlation.** If the team updates GUS asynchronously, the tool no longer flags stale GUS status as stale work or draws "GUS hygiene" conclusions.
- **CHANGELOG.md** to track changes across versions.

### Context
Based on tester feedback from Core on SAM team (19 members). Key issues: commit counts were wrong (5 reported vs 78 actual), sprint-start status flagged as alarming, and GUS conclusions didn't match team practices.

## v0.1.0 — 2026-04-25

Initial version. Two-part flow: automated data gathering + dashboard (Phase 1-2), then EM-driven analysis via prompt playbook (Phase 3-8).
