# Facilitation Notes — 2-Hour Workshop

Brief guide for facilitators running the flow optimizer exercise.

---

## Session Structure

| Time | Phase | What Happens | Facilitator Role |
|------|-------|-------------|-----------------|
| 0:00-0:05 | Intro | Explain the exercise: "You'll use AI to analyze your team's workflow and design an improvement experiment." | Set expectations. This is hands-on, not a lecture. |
| 0:05-0:25 | Part 1: Skill | EMs run `/flow-optimizer` to pull their team's data and see the flow dashboard. | Help with GUS access issues, team name resolution. |
| 0:25-0:35 | Transition | EMs review their dashboard. Facilitator highlights what to look for. | Walk the room. Point out patterns. "What do you see?" |
| 0:35-1:15 | Part 2: Playbook | EMs work through the 5 prompts at their own pace. | Circulate. Help stuck EMs. Push them to be specific. |
| 1:15-1:35 | Share & Discuss | 3-4 EMs share their experiment cards. Group discussion. | Facilitate. "What did you learn? What surprised you?" |
| 1:35-1:50 | Reflection | How would you run this with your team? What would you change? | Connect to learning objectives. |
| 1:50-2:00 | Close | Takeaways, next steps, feedback. | Reinforce: this is a pattern, not a one-time exercise. |

---

## Common Issues and How to Help

### "My team isn't in GUS" or "No data found"
- Check team name spelling (GUS is exact match)
- Try the EM email lookup approach
- If GUS truly has no data, the EM can still work through the playbook using their own knowledge — the prompts work without data, just with less specificity

### "The dashboard doesn't look right"
- Check if the team uses sprints (some teams use Kanban)
- Check if story points are used (some teams don't estimate)
- Missing cycle time fields are common — the skill should skip those sections gracefully

### "I'm stuck on Prompt 2 (root cause)"
- Suggest they ask Claude to pull supporting data: blocked items, review queue, rework indicators
- Remind them to push back: "That's the symptom, not the cause. Ask why again."
- If truly stuck, suggest they move to Prompt 3 and come back

### "The AI recommendations feel generic"
- Push the EM to be more specific about their bottleneck first
- Remind them to say "be more specific" or "walk me through the daily workflow"
- The recommendations are only as good as the problem definition

### "I can't finish in time"
- The experiment card is the must-have. If they're running behind, jump to Prompt 5.
- A partially filled experiment card is still valuable — they can refine it later.

---

## Key Phrases for Facilitators

- "What do you see in the data that surprises you?"
- "Is that what you expected, or does it tell a different story?"
- "That's the symptom — why does that happen on your team specifically?"
- "Be more specific — what would you do Monday morning?"
- "You just did a workflow analysis with AI. How would you run this with your team?"

---

## Learning Objectives Checklist

By the end of the session, each EM should be able to:

- [ ] Describe how work flows across their team's SDLC
- [ ] Identify at least one bottleneck backed by data
- [ ] Articulate a root cause (not just a symptom)
- [ ] Name a specific AI tool that could help (and how)
- [ ] Have a written experiment card ready to take back to their team
- [ ] Explain how they would repeat this exercise with their team
