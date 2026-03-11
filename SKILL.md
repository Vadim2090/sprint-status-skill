---
name: sprint-status
description: Generate Vadim's weekly sprint status for Sprint Status Sync (Wednesday). Pulls planned tasks from the Sprint Planning Notion page (BDSM section), checks completion status against local memory, and identifies unplanned work.
---

# /sprint-status — Sprint Status Update

## Trigger
User says: /sprint-status, "sprint status", "sprint update"

## Purpose
Generate Vadim's sprint status update for the Wednesday Sprint Status Sync meeting.
Outputs a ready-to-paste Notion block with task statuses.

## Procedure

### Step 1: Find the Latest Sprint Planning Note

Search Notion for the latest Sprint Planning page:
1. Use `notion-search` with query `Sprint Planning` (filter to pages only)
2. The page title format is: `DD-MM-YYYY // Sprint XX Planning`
3. The page lives under Meeting Notes (`c6216be44bfb4adaabc7d5336d7bac08`) in ImmCore workspace
4. Pick the most recent one by date in the title

**IMPORTANT:** Do NOT use `notion-query-meeting-notes` — Sprint Planning pages are regular Notion pages, NOT Granola meeting notes. Use `notion-search` to find the page, then `notion-fetch` to read it.

If no Sprint Planning page is found, ask the user for the URL.

### Step 2: Extract BDSM Tasks

Fetch the page content with `notion-fetch`.

Parse the **BDSM** section (heading: `# BDSM {toggle="true"}`). This section contains Vadim's marketing team tasks for the sprint.

Extract each top-level bullet as a task. Ignore:
- The callout block with dashboard link and weekly targets (keep targets as header context)
- Sub-bullets are context for the parent task, not separate tasks

### Step 3: Determine Task Statuses

For each BDSM task, check status against these local memory sources (in priority order):
1. `~/AI OS/memory/handoff.md` — latest session summary
2. `~/AI OS/memory/handoff-history.md` — all session history (scan entries dated within the sprint week)
3. `~/AI OS/MEMORY.md` — persistent memory (already loaded in context)

Assign one status per task:
- Done — Task completed or clear evidence of completion in memory
- In progress — Partially done, work started but not finished
- Not started — No evidence of work in memory

For **In Progress** tasks, also extract next steps from memory (handoff.md "Next Steps" section, handoff-history.md). Add them as sub-items under the task. If no next steps are found in memory, note which tasks are missing next steps and **ask the user** after presenting the full status output. Do NOT block the status output waiting for this — show the status first, then ask.

### Step 4: Identify Blockers

Scan the same memory sources for blockers — things that prevented or slowed planned work. Look for:
- Access issues (missing credentials, permissions, accounts)
- Dependencies on other people that weren't resolved
- Technical failures or tool limitations
- Anything explicitly flagged as "blocked" or "couldn't" in handoff notes

Only include real blockers that impacted planned tasks, not general wishlist items.

### Step 5: Find Unplanned Work ("Other")

Scan `handoff-history.md` for sessions dated within the current sprint week (Monday through today).
Identify completed or in-progress work that does NOT map to any BDSM task.
These go under the "Other" heading.

### Step 6: Auto-detect Sprint Number

Extract the sprint number from the page title (e.g., `11` from `09-03-2026 // Sprint 11 Planning`).

### Step 7: Output

Present the status update in this exact copy-paste-ready format:

```
## Sprint XX Status

### BDSM
- [ICON] Task description — brief explanation if needed
    - Next step 1 (for in-progress tasks only)
    - Next step 2
- [ICON] Task description
...

### Blockers
- Blocker description — which task it impacts and what's needed to unblock

### Other
- [ICON] Unplanned task description — brief explanation
...
```

Status icons (use actual Unicode, not text):
- Done = checkmark
- In progress = play button
- Not started = red circle

Example line:
```
- checkmark  Gather results Value Factory Experiment v1 and plan next iteration — killed after negative results, all profiles reverted to v3
```

### Output Rules
- Keep explanations short (1 sentence max), only add when status needs context
- If a task was killed/cancelled, mark as Done and explain
- For Done tasks, mention the key outcome if non-obvious
- **Link to artifacts:** If a Notion page, Google Sheet, or other artifact was created/updated as part of a task, include a hyperlink. If a task is marked In Progress or Done but you have no artifact link in memory, **ask the user** for the link before finalizing output.
- Group related unplanned work into single line items
- Write in English
- After showing the formatted output, say: **"Ready to paste into Notion."**

## Data Sources
- **Notion:** Sprint Planning pages (regular pages under Meeting Notes)
- **Local memory:** handoff.md, handoff-history.md, MEMORY.md
