# AI Opportunity Matrix

Maps SDLC stages to concrete AI recommendations triggered by data signals. This is reference material the EM draws on during the prompt playbook phase — not auto-applied by the skill.

Recommendations focus on where AI tools and agents can help teams: **increase productivity** (faster throughput, less toil), **improve quality** (fewer bugs, better reviews), and **deliver more innovation** (free up capacity from reactive work).

---

## Matrix

| SDLC Stage | Data Signal | AI Recommendation | Impact Area |
|------------|-----------|-------------------|-------------|
| **Requirements** | Large stories (8+ pts) carrying over | Use Claude Code to decompose epics into smaller stories with clear acceptance criteria | Productivity |
| **Requirements** | Stories bouncing back from QA or reopened | Use Claude Code to generate acceptance criteria and edge case checklists from story descriptions | Quality |
| **Spike & Design** | High investigation-to-story ratio | Use Claude Code for codebase exploration, dependency mapping, and design doc drafting | Productivity |
| **Spike & Design** | Long spike durations (high `Days_In_Progress__c` on investigations) | Use Claude Code to search for prior art, similar implementations, and relevant documentation | Productivity |
| **Code Development** | Low velocity relative to team size | Use Claude Code or Copilot for AI-assisted pair programming and code generation | Productivity |
| **Code Development** | High WIP per engineer | Use Claude Code to help developers context-switch efficiently by summarizing code state across multiple work items | Productivity |
| **Testing & Debugging** | Test Failure items as % of total work | Use Claude Code for test generation from code changes and test failure root cause analysis | Quality |
| **Testing & Debugging** | QA In Progress as bottleneck status | Use Claude Code to generate unit and integration tests, reducing manual QA load | Quality, Productivity |
| **Code Review & Pre-checkin** | Items stuck in Ready for Review | Use Claude Code `/review` skill as a mandatory first-pass before human review — humans focus on architecture and business logic | Productivity, Quality |
| **Code Review & Pre-checkin** | Review concentrated among 1-2 people | Use Claude Code reviews to enable more team members to review confidently, with AI as a safety net | Quality, Innovation |
| **Merge & QA** | QA sign-off delays | Use Claude Code to generate test coverage reports and automated verification checklists | Productivity |
| **Release** | Epics off track for build | Use Claude Code for scope assessment — analyze remaining work vs. time to feature freeze | Productivity |
| **Release** | Rising bug trend across build window | Use Claude Code to analyze bug patterns and identify systemic issues before release | Quality |
| **Maintain** | High bug-to-story ratio (30%+) | Use Claude Code + Columbo for automated gack analysis and fix suggestions | Quality, Innovation |
| **Maintain** | P0/P1 bugs with high age | Use Claude Code to draft initial investigation notes and suggest code areas to examine | Productivity |
| **Cross-cutting** | Team spending 30%+ on reactive work (bugs + investigations) | AI tools can absorb routine tasks (test writing, first-pass review, bug triage) to free capacity for innovation | Innovation |

---

## How to Use This Matrix

1. After identifying the bottleneck and root cause, look up the relevant SDLC stage
2. Check if any of the data signals match what the team's data shows
3. Use the recommendation as a starting point — adapt to the team's specific context
4. Always include: the specific tool, how to set it up, and what metric to track

## Adding New Entries

This matrix is designed to be extensible. As new AI tools become available or new data signals are identified, add rows. Each entry needs:
- The SDLC stage it applies to
- A specific, measurable data signal that triggers the recommendation
- A concrete recommendation (tool + action + what to measure)
- Which impact area it serves (Productivity, Quality, Innovation)
