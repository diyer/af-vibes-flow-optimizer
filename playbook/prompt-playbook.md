# Prompt Playbook — Phases 3-8

After the skill presents the flow dashboard (Phase 2), the EM takes over. These prompts guide them through identifying bottlenecks, finding root causes, and designing an improvement experiment.

**For the EM:** These are starter prompts, not scripts. Modify them, push back on Claude's answers, ask follow-ups. The goal is a conversation that ends with a concrete experiment.

**For the facilitator:** The EM should type these into Claude Code themselves. The skill has already loaded context (team data, dashboard) so Claude has the full picture. See `facilitation-notes.md` for timing guidance.

---

## Prompt 1: Identify the Bottleneck

**Type this into Claude:**
```
Looking at the data we just reviewed, where is work getting stuck on my team?
Which stage of the SDLC has the biggest bottleneck, and what's the impact
on our ability to deliver?
```

**What to look for in the response:**
- A specific SDLC stage identified (not vague "multiple issues")
- Connection to actual data points from the dashboard
- Impact stated in concrete terms (delays, carryover, quality)

**If the answer is too generic, push back:**
```
Be more specific. Which exact status are items piling up in, how many,
and how long are they sitting there?
```

**If you disagree with the bottleneck identified:**
```
I see what the data says, but in my experience the real bottleneck is
[your view]. Here's why: [your reasoning]. Factor that into the analysis.
```

---

## Prompt 2: Root Cause Analysis

**Type this into Claude:**
```
Why is work getting stuck at [the bottleneck from Prompt 1]?
Go 2-3 levels deep. Don't just tell me the symptom — tell me why it
happens and why that underlying cause persists.
```

**What to look for:**
- 2-3 levels of "why" — observable → structural → systemic
- Root causes connected to data (not just theory)
- Causes that are actionable, not just "we need more people"

**If the answer stays surface-level:**
```
That's the symptom, not the cause. Why does [that thing] happen?
What's the structural reason it keeps recurring?
```

**If you want to explore the data more:**
```
Can you pull the blocked items data or the review queue to help us
understand this root cause better?
```

---

## Prompt 3: Define the Future State

**Type this into Claude:**
```
If we fixed this bottleneck, what would better look like?
Give me specific, measurable goals we could target — not vague improvements,
but numbers we can track.
```

**What to look for:**
- Measurable targets (e.g., "reduce Ready for Review time from 6 days to 2 days")
- Realistic timeframes (sprints, not days)
- Goals the EM actually cares about (the skill presented options in Phase 2 — which metrics did the EM say mattered?)

**If the goals feel too ambitious:**
```
That's a big target. What would a realistic first improvement look like
over the next 2-3 sprints?
```

**If the goals are too vague:**
```
"Improve code review speed" isn't measurable. What specific number would
tell us we're making progress? What's it at today?
```

---

## Prompt 4: Explore AI Opportunities

**Type this into Claude:**
```
Where could AI tools or agents help us get from our current state to
that future state? Be specific — which tools, how to set them up,
and what would we track to know it's working?

Think about where AI could help us increase productivity, improve quality,
or free up capacity for more innovation.
```

**What to look for:**
- Specific tool recommendations (not "use AI for testing")
- Setup steps (how would we actually start using this?)
- Connection to the identified bottleneck and root cause
- A clear metric to track

**If the recommendations are generic:**
```
"Use Claude Code for code review" is too vague. Walk me through exactly
how my team would use it — what does the workflow look like day to day?
What changes for the developer and the reviewer?
```

**If AI doesn't seem like the right fix:**
```
For this specific bottleneck, is AI actually the right tool? Or is this
more of a process/people/prioritization issue? Be honest.
```

---

## Prompt 5: Design the Experiment

**Type this into Claude:**
```
Help me design an improvement experiment. I want to test one or two changes
over the next [2-3 sprints / 4-6 weeks].

Use this format:
- Background: why we're doing this
- Current State: the key metrics today
- The Problem: one sentence
- Root Cause: 2-3 levels
- Future State: measurable goals
- What We'll Try: 1-2 specific actions with concrete first steps
- Duration: how long we'll run the experiment
- How We'll Know It's Working: metrics, check-in cadence, when to reassess

My first step this week should be something I can do Monday morning.
```

**What to look for:**
- Complete experiment card (all sections filled)
- Actions are specific enough to start immediately
- Duration is defined
- Success metrics are measurable
- The "first step this week" is concrete and actionable

**If the experiment tries to do too much:**
```
That's too many changes at once. Pick the one action most likely to
make a difference and let's focus on that.
```

**If the first step isn't concrete enough:**
```
"Implement WIP limits" isn't a Monday morning action. What specifically
would I say or do in my next standup or planning session?
```

---

## After the Experiment Card

Once the EM has their experiment card:

```
Now that you have your experiment, a couple of things to think about:

1. **Share it with your team.** An experiment works better when the team
   understands the why, not just the what.

2. **Check progress at each sprint retro.** Are the metrics moving?
   If not after 2 sprints, the hypothesis may be wrong — that's okay,
   adjust and try again.

3. **Re-run this analysis.** Come back to Claude Code and ask it to pull
   your flow data again after the experiment ends. Compare the before
   and after.

4. **Run this with your team.** You just went through a workflow analysis
   exercise. You can do the same thing with your team — have them look
   at the data together and design their own experiments.
```

---

## Quick Reference: All 5 Prompts

| # | Purpose | Key Phrase |
|---|---------|-----------|
| 1 | Find the bottleneck | "Where is work getting stuck?" |
| 2 | Root cause | "Why does it happen? Go 2-3 levels deep." |
| 3 | Future state | "What does better look like? Give me numbers." |
| 4 | AI opportunities | "Where could AI tools help? Be specific." |
| 5 | Design experiment | "Help me design an experiment using this template." |
