# TeamOS (fdeOS), Team Operating System

A Claude Code framework for Forward Deployed Engineers (FDEs) to manage multiple customer engagements: standardized tracking files, Slack/Google Drive auto-sync, and generated reports (weekly trackers, barrier reports, standup agendas), all driven through Claude Code skills rather than a separate app.

## Overview

TeamOS (internally also called "fdeOS") solves a specific problem: FDEs juggle several customer engagements at once, each with its own stakeholders, blockers, and timeline, and leadership needs consistent, timely rollups across all of them. Instead of a database or web app, TeamOS keeps each engagement's state in a small set of structured Markdown files under `customers/[customer-name]/`, and uses Claude Code skills to read, update, and report on those files. There is no server, no build step, and no deployment, you clone/copy the folder and start running skills against it in Claude Code.

## Listing Metadata

- **Industries:** Cross-industry (internal use, Forward Deployed Engineering teams)
- **Horizontal Product:** Productivity / Collaboration
- **Business Need:** Standardized tracking and reporting across multiple customer engagements for FDEs, weekly trackers, barrier reports, standup agendas
- **Requires:** Claude Code; optionally Google Drive and Slack for auto-sync
- **Compatible With:** Google Drive, Slack
- **Salesforce Editions:** Not applicable, not a Salesforce package; operates entirely via Markdown files and Claude Code skills

**App Details**
- Version: tracked via git history (no separate release versioning)
- Package Contents: not applicable, no Salesforce metadata; Markdown + Claude Code skills only
- Languages: English

**Security:** Not applicable, not listed on AppExchange.

## Architecture

```
TeamOS/
├── CLAUDE.md                # System-level instructions Claude reads on every session
├── .claude/skills/          # Skill implementations (the "application logic")
│   ├── project-setup/       # Scaffolds a new customer/ folder from templates/
│   ├── new-agent-workstream/# Converts simple → complex structure
│   ├── project-sync/        # Pulls Google Drive / Slack content, updates tracking files
│   ├── fde-tracker-submit/  # Generates the weekly Momentum Program form submission
│   ├── barrier-report/      # Aggregates BARRIERS.md across all customers
│   ├── weekly-update-account-team/ # Generates a Slack-ready weekly update
│   └── standup/             # Generates a daily standup agenda from project state
├── templates/project/       # *.md.template files copied into new customer/ folders
└── customers/                # Per-customer state (gitignored, see Configuration)
    └── [customer-name]/
        ├── CLAUDE.md, AGENTS.md, STAKEHOLDERS.md, BARRIERS.md, LEARNINGS.md, TIMELINE.md
        ├── updates/, standups/, weekly-updates/
        └── agents/[workstream]/  (only for "complex" structure engagements)
```

There is no separate persistence layer or database, **the Markdown tracking files under `customers/` are the memory.** Claude reads them at the start of a skill invocation and writes back to them at the end. Two structures are supported depending on engagement complexity (see [Choosing project structure](#choosing-project-structure)), and a project can migrate from simple to complex in place without losing data.

## Features

- Consistent, template-driven tracking of stakeholders, barriers, learnings, and timeline per customer engagement
- Auto-sync from Google Drive (meeting transcripts) and Slack into structured tracking files, with a dated change log per sync
- Generated, form-ready weekly tracker submissions (Momentum Program), leadership barrier reports, Slack weekly updates, and standup agendas
- Shareable framework: the skills/templates are meant to be copied between FDEs, while each person's customer data and profile stay private (see [Sharing TeamOS](#sharing-teamos))

## Prerequisites

- [Claude Code](https://docs.claude.com/claude-code) CLI.
- A global user profile at `~/.claude/memory/user_profile.md` (name, role, team, communication preferences), TeamOS skills read this for personalization, but it isn't provisioned by this repo.
- Access to whatever Google Drive folders and Slack channels a given customer engagement references (configured per-customer, not globally, see Configuration).

## Installation / Setup

```bash
# 1. Get the framework onto your machine (copy or clone into your workspace)
cd ~/TeamOS   # or wherever you keep it

# 2. One-time: set up your personal profile
# Tell Claude: "set up my TeamOS profile", it creates ~/.claude/memory/user_profile.md
```

There is no package manager, dependency install, or build step, TeamOS is a directory of Markdown templates and Claude Code skills, not a compiled or served application.

## Configuration

TeamOS has no global environment variables or secrets file. Configuration is per-customer, stored directly in that customer's own tracking files:

| Setting | Where | Required for |
|---|---|---|
| M120 Slack channel (Momentum Program account channel) | `customers/[name]/CLAUDE.md` and `AGENTS.md` | `/fde-tracker-submit` |
| Google Drive folder link | `customers/[name]/CLAUDE.md` | `/project-sync` |
| Additional Slack channels | `customers/[name]/CLAUDE.md` | `/project-sync` |

Google Drive and Slack access are provided through whatever MCP connectors/integrations are configured in your Claude Code environment, TeamOS itself does not manage authentication.

**Never commit `customers/`.** It's excluded via `.gitignore` (`customers/*/` with a `!customers/.gitkeep` exception to preserve the folder structure) precisely because it holds real customer engagement data. If you find yourself about to override that, stop and reconsider.

## Usage

All usage is conversational, through Claude Code skills, there's no CLI binary to invoke directly.

```text
"Set up a new project for Acme Corp"        → /project-setup
"Sync the project"                          → /project-sync (run from inside customers/acme-corp/)
"/fde-tracker-submit"                       → weekly Momentum Program tracker submission
"Generate barrier report"                   → /barrier-report, aggregated across all customers
"Generate weekly update"                    → /weekly-update-account-team
"Prep standup" / "what should I bring up"   → /standup
```

Recommended cadence: `/project-sync` daily Monday–Wednesday (each run creates `updates/YYYY-MM-DD-sync.md`), then `/fde-tracker-submit` Thursday morning to roll the week's change logs into a form-ready submission.

## Choosing project structure

**Simple** (1–2 agents sharing stakeholders/timeline/barriers):
```
customers/acme-corp/
├── CLAUDE.md, AGENTS.md, STAKEHOLDERS.md, BARRIERS.md, LEARNINGS.md, TIMELINE.md
└── updates/
```

**Complex** (3+ agents with independent workstreams):
```
customers/acme-corp/
├── STAKEHOLDERS.md, TIMELINE.md   # customer-level, shared
└── agents/
    ├── sales-forecasting/{AGENTS,BARRIERS,LEARNINGS}.md, updates/
    └── service-automation/...     # same structure
```

Start simple; `/new-agent-workstream` converts to complex automatically when needed (data is moved, not deleted).

## Project structure (full reference)

See [Architecture](#architecture) for the top-level layout. Per-customer tracking files, in detail:

- **CLAUDE.md**, strategic context: objectives, constraints, key decisions, workflows, M120 channel
- **AGENTS.md**, agent metadata for tracker submissions: types, phases, status, go-live dates, sentiment, implementation partners
- **STAKEHOLDERS.md**, who's involved, communication styles, concerns, decision authority
- **BARRIERS.md**, blockers organized by product area, with impact and next steps
- **LEARNINGS.md**, successes ✅, challenges ⚠️, patterns 💡, retrospectives 🔄
- **TIMELINE.md**, week-by-week narrative, not just a date list
- **updates/**, **standups/**, **weekly-updates/**, generated/dated output, not hand-authored

## Testing

There is no automated test suite. TeamOS is a set of Markdown templates and Claude Code skill prompts, not executable application code, correctness is verified by running each skill against a real or sample customer folder and reviewing the generated output.

## Deployment

Not applicable in the traditional sense, there's nothing to build or deploy. "Deploying" TeamOS to a new machine or teammate means copying the framework (see [Sharing TeamOS](#sharing-teamos)) and running the one-time profile setup.

## Sharing TeamOS

✅ Share: the framework itself, skills, templates, documentation.
❌ Don't share: your `~/.claude/memory/` (personal profile) or your `customers/` folder (real engagement data).

Each person who adopts TeamOS keeps their own profile and their own `customers/` folder; only the framework (skills + templates) is common.

## Contributing

This is an internal FDE tool, evolved by whoever's using it. There's no formal review process, customize templates for your engagement types, add skills for workflows you find yourself repeating, and adjust the simple/complex structures to match your team's needs. If you change a skill's behavior in a way that affects shared customers/ file formats, update the corresponding `templates/project/*.md.template` to match.

## License

[MIT](LICENSE) © 2026 VurtuoLabs

## Tips

1. Sync regularly, weekly at minimum, ideally after major meetings.
2. Keep `BARRIERS.md` current as blockers resolve; stale barriers make leadership reports misleading.
3. Capture learnings as decisions happen, not in retrospect.
4. Let auto-sync update files, then review the change log rather than editing tracking files by hand.
5. Generate reports often, leadership prefers frequent, timely updates over comprehensive-but-late ones.
