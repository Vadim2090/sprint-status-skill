# /sprint-status — Claude Code Skill

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill that generates sprint status updates by cross-referencing planned tasks from Notion with local session memory.

## What it does

1. Finds the latest Sprint Planning page in Notion
2. Extracts your planned tasks (from a designated section)
3. Checks completion status against local memory files (`handoff.md`, `handoff-history.md`, `MEMORY.md`)
4. Identifies unplanned work completed during the sprint
5. Outputs a ready-to-paste status update with task statuses and links to Notion artifacts

## Output format

```
## Sprint 11 Status

### BDSM
- ✅  Task A — completed, key outcome
- ▶️  Task B — framework created, interview scheduled
- 🔴  Task C

### Other
- ✅  Unplanned task D — deployed to production
- ▶️  Unplanned task E — in progress
```

## Installation

Copy `SKILL.md` into your Claude Code skills directory:

```bash
mkdir -p ~/.claude/skills/sprint-status
cp SKILL.md ~/.claude/skills/sprint-status/skill.md
```

Or clone the repo:

```bash
git clone https://github.com/Vadim2090/sprint-status-skill.git
cp sprint-status-skill/SKILL.md ~/.claude/skills/sprint-status/skill.md
```

## Configuration

The skill assumes:
- Sprint Planning pages live in Notion as regular pages (not Granola meeting notes) with title format `DD-MM-YYYY // Sprint XX Planning`
- Tasks are in a section called **BDSM** (toggle heading)
- Session memory is stored in `~/AI OS/memory/` (handoff.md, handoff-history.md)

Edit `SKILL.md` to adjust paths, section names, and Notion workspace references for your setup.

## Usage

In Claude Code:
```
/sprint-status
```

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) with Notion MCP integration
- Sprint Planning pages in your Notion workspace
- Local session memory files (handoff.md pattern)

## Contributing

PRs welcome. If you adapt this for a different project management setup, consider adding your variation as an alternative config in the skill.

## License

MIT
