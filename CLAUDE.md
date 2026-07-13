# fdeOS - FDE Operating System

This is the FDE Operating System (fdeOS) for managing customer engagements and projects using Claude Code.

## System Overview

fdeOS provides a standardized structure and workflow for managing multiple customer projects with:
- **Momentum Templates**: Pre-built project structure for consistency
- **Tracking Files**: Persistent memory for stakeholders, barriers, learnings, and timelines
- **Auto-sync Skills**: Skills that pull updates from Google Drive and Slack
- **Report Generation**: Dynamic reporting from tracking files

## User Context

Claude will read your personal profile from global memory (`~/.claude/memory/user_profile.md`).
This includes your name, role, team, and communication preferences.

## How fdeOS Works

### Project Structure

fdeOS supports two structures depending on engagement complexity:

#### Simple Structure (1-2 agents, shared context)
```
customers/[customer-name]/
├── CLAUDE.md          # Strategic context, objectives, constraints, M120 channel
├── AGENTS.md          # Agent metadata (types, phases, go-live dates, sentiment)
├── STAKEHOLDERS.md    # Rich profiles with communication styles
├── BARRIERS.md        # Product-area organized blockers
├── LEARNINGS.md       # Successes ✅, challenges ⚠️, patterns 💡, retrospectives 🔄
├── TIMELINE.md        # Week-by-week narrative with context
├── updates/           # Daily sync change logs (YYYY-MM-DD-sync.md)
├── standups/          # Daily standup notes (YYYY-MM-DD.md)
└── weekly-updates/    # Generated reports (stakeholder-facing)
```

**Use when:** 1-2 agents share stakeholders, timelines, and barriers

#### Complex Structure (3+ agents, different contexts)
```
customers/[customer-name]/
├── CLAUDE.md          # Customer-level strategic context
├── STAKEHOLDERS.md    # Customer-level stakeholders (shared)
├── TIMELINE.md        # Customer-level timeline (shared)
└── agents/
    ├── [workstream-1]/
    │   ├── AGENTS.md
    │   ├── BARRIERS.md
    │   ├── LEARNINGS.md
    │   └── updates/
    └── [workstream-2]/
        └── ... (same structure)
```

**Use when:** Multiple independent workstreams with separate teams, timelines, or M120 channels

**Migration:** Start simple → convert to complex automatically via `/new-agent-workstream` when needed

### Available Skills

- **/project-setup**: Create new customer project (choose simple or complex structure)
- **/new-agent-workstream**: Add agent workstream to project (converts simple → complex if needed)
- **/project-sync**: Sync project from Google Drive/Slack resources
- **/fde-tracker-submit**: Generate FDE weekly tracker submission (Momentum Program form)
- **/barrier-report**: Generate leadership report from all barriers
- **/weekly-update-account-team**: Generate workstream-based weekly update for Slack
- **/standup**: Generate daily standup agenda from project state

### Workflows

#### Creating New Project
1. User: "Set up a new project for [Customer Name]"
2. Claude runs `/project-setup`
3. Creates customer folder with all tracking files from templates
4. Prompts for: customer name, engagement type, **M120 Slack channel**, key resources

#### Syncing Project
1. User: "Sync the project" (while in customer directory)
2. Claude runs `/project-sync`
3. Reads CLAUDE.md for resource locations
4. Pulls recent content from Google Drive/Slack (last 7 days default)
5. Updates tracking files automatically
6. Creates change log in `updates/YYYY-MM-DD-sync.md` with weekly deltas

#### Submitting Weekly Tracker (Momentum Program)
1. User: "Generate tracker submission" (Thursday morning, weekly cadence)
2. Claude runs `/fde-tracker-submit`
3. Reads `AGENTS.md` for stable metadata (agent types, go-live dates, sentiment)
4. Scans `updates/` for change logs from last 7 days
5. Generates form-ready output with exact field mapping to Momentum Program form
6. Saves to `weekly-updates/fde-tracker-submit-YYYY-MM-DD.md`
7. User copies each field directly into web form

**Weekly Workflow:**
```bash
# Monday-Wednesday: Daily syncs create change logs
sync the project  # Creates updates/2026-05-05-sync.md
sync the project  # Creates updates/2026-05-06-sync.md

# Thursday morning: Generate tracker submission
/fde-tracker-submit  # Reads AGENTS.md + all week's change logs
```

#### Generating Reports
1. User: "Generate barrier report"
2. Claude runs `/barrier-report`
3. Reads all `customers/*/BARRIERS.md` files
4. Aggregates by product area
5. Outputs formatted report (or posts to Slack)

## Memory System

**Persistent memory lives in tracking files**, not in Claude's conversation memory:
- **CLAUDE.md** = strategic context, objectives, constraints, key decisions
- **AGENTS.md** = agent metadata (types, phases, status, go-live dates, sentiment, implementation partners)
- **STAKEHOLDERS.md** = who's involved, how they communicate, what they care about
- **BARRIERS.md** = what's blocking progress, impact, owners, next steps
- **LEARNINGS.md** = what's working, what's not, patterns discovered, retrospectives
- **TIMELINE.md** = narrative of what happened, when, and why
- **updates/** = weekly delta change logs from project sync operations

Claude reads these files to understand project state, history, and dynamics.

**Template Quality**: fdeOS templates enhanced based on best practices from mature FDE engagements:
- Strategic context and constraints in CLAUDE.md
- Communication styles and decision authority in STAKEHOLDERS.md
- Narrative timeline format (not just dates)
- Structured learnings: successes ✅, challenges ⚠️, patterns 💡, retrospectives 🔄
- Detailed barrier tracking with specific impact and next steps

## Sharing fdeOS

This entire fdeOS folder can be shared with colleagues:
- ✅ Structure, templates, skills are universal
- ✅ Each person's user profile stays in their global memory
- ❌ Don't share the `customers/` folder (contains project data)
- ✅ Colleagues copy fdeOS, then create their own customer projects
