# How to run the build with /goal

The build runs as repeated **30-minute sessions**. You paste the same `/goal` each time.
Each session continues from `docs/PROGRESS.md`, stops after about 30 minutes, and
reports what it did, how much it used, and what remains. You then decide whether to
continue.

## One-time setup in each new Claude Code session
1. `/model sonnet`: the main session (implementation) runs on Sonnet 5.5. Set effort
   to **medium** in the `/model` picker (or with `/effort medium` if your version
   has it).
2. Switch the permission mode to **auto** so sessions run without asking for approval.
   Opus 5.5 at medium effort is called automatically, as subagents, for planning,
   design and validation (see `docs/BUILD_PROTOCOL.md` §1).

## The session goal (paste this every time)

```
/goal Run ONE ~30-minute build session for the app in docs/APP_SPEC.md, following docs/BUILD_PROTOCOL.md exactly. Read BUILD_PROTOCOL.md, APP_SPEC.md and PROGRESS.md first, then continue from the first unchecked task in PROGRESS.md.
Models: planning/design and checks/validation use Opus 5.5 at medium effort via subagents (Agent model "opus", effort "medium"); implementation uses Sonnet 5.5 at medium effort (this main session, or Agent model "sonnet", effort "medium" if this session is not on Sonnet). Very high UI quality per spec 4.11 is a hard requirement.
Done when ALL of these are shown in this conversation:
1. `date -u` output from session start and from the end shows at least 30 minutes elapsed, OR every milestone in PROGRESS.md is ticked off.
2. No new task was started after the 25-minute mark.
3. The work done this session is committed and pushed, and `git status` is clean.
4. PROGRESS.md has a new session report in the BUILD_PROTOCOL §3 format (done, tests, estimated tokens used, remaining work, estimated remaining sessions and tokens, blockers, next task), and the same report is printed in the conversation.
5. No background task or subagent is still running.
Never edit APP_SPEC.md. Stop after 40 turns.
```

## After each session
- Read the session report in the chat.
- Type `/goal` (no arguments) to see the **exact token use** of the session that just
  finished. Check how much of your plan you've used under claude.ai → Settings →
  Usage.
- To continue, paste the same `/goal` again. To pause, just stop. Everything is
  committed, and the next session picks up from PROGRESS.md.
- To change direction, edit PROGRESS.md (reorder or remove tasks) or tell Claude
  before the next session.

## Notes
- Claude cannot read your subscription's usage meter. The report's token figures
  are estimates; `/goal` status and the Usage page show the real numbers.
- A session that hits a usage limit pauses and resumes when the limit resets. The
  30-minute clock then includes the waiting time, so check the report.
