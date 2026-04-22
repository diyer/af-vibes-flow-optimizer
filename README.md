# Flow Optimizer

A Claude Code skill that helps Engineering Managers analyze their team's workflow, identify bottlenecks, and design improvement experiments — using real data from GUS and Git.

## What It Does

The Flow Optimizer pulls your team's actual data from two sources:

- **GUS** — sprint velocity, status distribution, work type mix, stale items, carryover, cycle time, epic health
- **Git** — commit velocity, author distribution, merge patterns, review concentration, hotfix frequency

It combines both into a flow dashboard mapped to 8 SDLC stages, shows where work gets stuck, and then guides you through a structured conversation to identify root causes and design a concrete experiment you can take back to your team.

## Setup

1. Clone this repo:
   ```
   git clone https://git.soma.salesforce.com/stacy-gordon/flow-optimizer.git
   ```

2. Go into the folder:
   ```
   cd flow-optimizer
   ```

3. Launch Claude Code:
   ```
   claude
   ```

4. Authenticate with GUS (first time only):
   ```
   /salesforce-trust-foundations:mcp-auth
   ```

5. Start the skill:
   ```
   /flow-optimizer
   ```

## What to Expect

The skill runs in two parts:

**Part 1 — Data gathering and dashboard (~15 min).** The skill asks for your team name and repos, pulls GUS and Git data, and presents a flow dashboard showing where work is piling up across your SDLC.

**Part 2 — Analysis and experiment design (~15-20 min).** You work through a series of prompts to identify the biggest bottleneck, dig into root causes, explore where AI tools could help, and design an improvement experiment with a concrete first step.

You'll walk away with an experiment card you can share with your team and implement right away.

## Navigation

You can say any of these at any point during the session:

- **"skip"** — Move to the next phase
- **"back"** — Return to a previous phase
- **"show me the data"** — Run additional queries
- **"give me the experiment card"** — Jump to experiment design

## Requirements

- Claude Code installed and configured
- GUS access (via sf CLI with `--target-org gus`)
- Git credentials configured for your team's repos (git.soma, gitcore, or github.com)
