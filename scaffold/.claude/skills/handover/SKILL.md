---
name: handover
description: Record the current in-flight state and next steps in HANDOVER.md, then stop, so a fresh session can resume with "continue". Use when the user says "handover", when a usage limit or long context is near, or before the user runs /clear.
---

# Handover

Goal: a fresh session that reads only `HANDOVER.md` can continue
the work without asking what happened.

1. Stop launching new work. Let running subagents finish if they are close;
   otherwise note them as in flight.
2. Collect the facts (do not guess):
   - `git status --short`, current branch, `git log --oneline -5`
   - open PRs for this branch, if a forge CLI is available
   - which tickets are in progress and their last decision
3. Update `HANDOVER.md` (keep it short; newest state at the top):
   - **State**: HEAD commit, branch, what is committed, what is not.
   - **In flight**: each unfinished task, which files it touches, and
     **how to resume it** ("rerun X, commit if green", "check git diff first").
   - **Waiting on the user**: open questions and decisions needed.
   - **Gotchas**: anything a fresh session would trip on.
4. Update **Next steps** in the same file: remove finished tasks (log them in the
   ticket), keep the rest in priority order. If the project split them out into
   `NEXT.md`, update that file instead.
5. If the tracker is external (Jira, GitHub Issues), add a short progress
   comment on each in-progress ticket.
6. Reply with one line: what was recorded and that it is safe to `/clear`.

Do not commit unless the project's workflow agreement says handover commits
are allowed.
