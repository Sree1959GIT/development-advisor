# Build protocol: rules every /goal session follows

Read this file, `docs/APP_SPEC.md` and `docs/PROGRESS.md` at the start of every
session. `docs/PROGRESS.md` is the single source of truth for what is done and what is
next.

## 1. Models and roles

| Work | Model | How |
|---|---|---|
| Planning and design (milestone plans, architecture, `DESIGN_SYSTEM.md`, UI design) | **Opus 5.5, medium effort** | Spawn an Agent with `model: "opus"`, `effort: "medium"`. It returns the plan or design, and the main session writes it to `docs/`. |
| Implementation (code, tests, fixes, docs) | **Sonnet 5.5, medium effort** | Done in the main session, which the user starts with Sonnet 5.5 at medium effort. If the main session is not running Sonnet, delegate each implementation task to an Agent with `model: "sonnet"`, `effort: "medium"`. |
| Checks and validation (milestone acceptance, code review, UI review) | **Opus 5.5, medium effort** | Spawn an Agent with `model: "opus"`, `effort: "medium"`. It runs the tests, reads the diff and screenshots, and returns findings. It must not edit files; the main session fixes the findings. |

Keep usage low:
- Only call Opus at milestone start (plan), after a large feature (review) and at
  milestone end (acceptance). Never call it for routine coding.
- Give every subagent a short, self-contained brief: the files to read, the question,
  and the expected output format. Never ask it to re-read the whole repo.
- Don't re-read files that are already in context. Use targeted `grep` or `sed -n`
  ranges. Run only the tests you need while iterating, and the full suite before
  committing.

## 2. Session timing (30-minute sessions)

1. At session start, run `date -u` and record the start time in the PROGRESS.md
   "Current session" block.
2. Work through the next unchecked tasks in PROGRESS.md in order. Run `date -u`
   after each task.
3. Once **25 minutes** have passed, don't start a new task. Finish or cleanly park the
   current one: commit working code, or note exactly where it stopped.
4. At **30 minutes or more**, write the session report (section 3), then commit and
   push. Make sure no background tasks or subagents are still running.

## 3. Session report (append to PROGRESS.md "Session log" and print in chat)

```
### Session N: <date> <start>-<end> UTC (<minutes> min)
Done: <tasks completed, with commit SHAs>
Tests: <suites run and results, e.g. pytest 142 passed; coverage 83%>
Usage this session: ~<tokens> tokens (estimate: Opus calls x size + main-session work);
  check the exact figure with `/goal` status
Remaining: <milestones/tasks left>
Estimated remaining effort: ~<N> sessions, ~<tokens> tokens
Blockers / decisions needed: <none | list>
Next session starts with: <task>
```

Estimate remaining effort from the average tokens and tasks per finished session so
far. Before any session has finished, use 1 session ≈ 1–2 tasks.

## 4. Git discipline

- Commit after every completed task with a clear message. Push at the end of every
  session to the designated branch.
- Never commit secrets, model weights, or large generated data. Keep generated
  fixtures under 5 MB.

## 5. Quality bar

- Tests are written alongside code (pytest, Vitest, Playwright). A task is done only
  when its tests pass.
- The UI follows `docs/DESIGN_SYSTEM.md` and spec section 4.11. Every UI task ends with
  Playwright screenshots (light and dark) and an axe check.
- Every milestone ends with an Opus acceptance check against its "Done when" list in
  PROGRESS.md. Findings are fixed before the milestone is ticked off.
- Never edit `docs/APP_SPEC.md` to make a check pass. Record unavoidable deviations in
  `docs/DECISIONS.md`.
