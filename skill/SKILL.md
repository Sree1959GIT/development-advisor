---
name: development-advisor
description: |
  Project continuity & restart assistant. Your first action after opening a project or taking a break.
  Analyzes your project's git history, previous sessions, checkpoints, and current state to provide
  complete reorientation. Shows what you were working on, where you left off, what blockers you hit,
  and the optimal way to resume. Includes strategic guidance on session management, architecture,
  testing, and next steps.

  ALWAYS use this skill when:
  - You're restarting work after a break (next day, after a few hours, after a week)
  - You're returning to a project after switching to other work
  - You've switched between multiple branches/sessions and lost context
  - You need to understand current project state before deciding how to proceed
  - You're unsure what to do next or what was blocking you
  - You want a strategic assessment before major decisions (refactor, architecture, testing)
  - You want to assess session health and decide fork vs resume vs restart
compatibility: Claude Code with git access and previous session history
---

## How the Advisor Works

When you invoke `/advisor` (or `/advisor "Help me get restarted"`), you're asking for complete project context recovery and strategic guidance.

The advisor:
1. **Recovers Project History** — Analyzes git commits, previous session summaries, checkpoints
2. **Assesses Current State** — Identifies blockers, pending decisions, test status, incomplete work
3. **Provides Reorientation** — Explains what you were doing and where you left off
4. **Recommends Optimal Resume Path** — Decides whether to resume session, fork, or start fresh
5. **Lists Immediate Next Steps** — Gives you specific actions to take right now
6. **Provides Strategic Guidance** — Architecture, testing, and design advice

The advisor runs in the background as an independent subagent, so it doesn't consume your main session's context window.

## Usage

### Restart After a Break (PRIMARY)
```
/advisor "Help me get restarted"
```
Complete project reorientation with what you were working on, where you left off, blockers, and optimal resume path.

### Quick Status Check
```
/advisor
```
Current session health assessment and immediate recommendations.

### Specific Questions
```
/advisor "should I fork this session before starting the refactor?"
/advisor "we keep hitting the same error, what should I do?"
/advisor "is this a good time to delegate to a subagent?"
```

## What You'll Get

- What you were working on (feature, branch, status)
- Where you left off (commits, files modified, session state)
- Blockers and pending decisions
- Project context (architecture patterns, testing status)
- How to resume (recommended approach with reasoning)
- Immediate next steps (3-4 specific actions)
- Ready-to-run commands (copy-paste ready)

## When to Use

- When restarting after a break (your main use case)
- When uncertain about next steps
- When stuck or blocked
- Before major decisions (refactors, architecture)
- For session health checks

## Installation

```bash
mkdir -p ~/.claude/skills/development-advisor
cp skill/SKILL.md ~/.claude/skills/development-advisor/
# Restart Claude Code
```

## More Information

See `docs/` directory for complete guides, examples, and troubleshooting.
