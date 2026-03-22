# /karpathy — Autonomous Research Skill for Claude Code

Inspired by [Karpathy's autoresearch](https://github.com/karpathy/autoresearch). An autonomous experimentation loop that continuously optimizes any measurable metric through systematic experimentation: **modify -> measure -> keep/discard -> repeat**.

## How It Works

The skill deploys a 4-agent team via Claude Code Agent Teams:

| Agent | Role | Model |
|-------|------|-------|
| **karpathy-lead** | Orchestrates the loop, manages state, reports progress | Opus |
| **karpathy-researcher** | Investigates context, proposes experiments | Sonnet |
| **karpathy-executor** | Implements changes in isolated worktrees, measures results | Sonnet |
| **karpathy-analyst** | Evaluates results, decides keep/discard, tracks learnings | Sonnet |

## Supported Domains

- **Web Performance** — Lighthouse scores, Core Web Vitals, bundle size
- **Ad Optimization** — CTR, ROAS, A/B testing (Facebook Ads, Google Ads)
- **Code Quality** — Test coverage, build time, type errors, lint warnings
- **Custom** — Any domain with a measurable metric

## Installation

### Prerequisites

- Claude Code with Agent Teams enabled
- Set `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` in `~/.claude/settings.json`

### Install

Copy files to your Claude Code config:

```bash
# Agents
cp agents/*.md ~/.claude/agents/

# Skill
mkdir -p ~/.claude/skills/karpathy/program_templates
cp skill/SKILL.md ~/.claude/skills/karpathy/
cp skill/program_templates/*.md ~/.claude/skills/karpathy/program_templates/
```

## Usage

From any project in Claude Code:

```
/karpathy
```

Then describe what you want to optimize:

- *"/karpathy optimize the Lighthouse score for this site"*
- *"/karpathy run A/B testing on Facebook Ads CTR"*
- *"/karpathy improve test coverage in this repo"*
- *"/karpathy optimize response time for the /api/search endpoint"*

The skill auto-detects the domain, creates a team, establishes a baseline, and starts the infinite experimentation loop.

## The Loop

```
INIT: detect domain -> define metric -> measure baseline -> create team

LOOP (infinite):
  1. Researcher proposes experiment
  2. Lead validates proposal
  3. Executor implements in worktree + measures
  4. Analyst evaluates: keep or discard?
  5. Log results to results.tsv
  6. Report progress every 3 experiments
  7. Repeat
```

## Core Concepts (from autoresearch)

| autoresearch | /karpathy |
|---|---|
| `train.py` (modifiable) | Target files (parametric) |
| `prepare.py` (invariant) | Project infrastructure (don't touch) |
| `program.md` (agent instructions) | Domain template + user context |
| `val_bpb` (metric) | Domain metric (Lighthouse, CTR, coverage, etc.) |
| `results.tsv` (log) | `results.tsv` (same format, extended) |
| git branch + reset | git worktree per experiment |
| single agent | 4-agent team |

## License

MIT
