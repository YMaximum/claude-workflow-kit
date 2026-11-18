# Handover and checkpoints

Sessions end unexpectedly: a usage limit, an interruption, or a context so long it
gets slow and expensive. The fix: **write state down continuously**, so any fresh
session can pick up with one word.

## The files
| File | Holds | Changes |
|---|---|---|
| `HANDOVER.md` | **State** (branch, HEAD, uncommitted work), **In flight** (what is running and **how to resume each item**), **Next steps** (one line per task, in priority order), **Waiting on the user**, **Gotchas** | after every commit and every subagent result |
| the ticket | decisions and progress per ticket | at every step boundary |

Template: `scaffold/HANDOVER.md`. Skill: `scaffold/.claude/skills/handover/`.

## The loop
1. Claude works on a ticket.
2. After each commit or subagent result, Claude updates `HANDOVER.md`.
3. The session stops (limit hit, interrupted) **or** the user says `handover`
   → Claude records in-flight work and stops. Then `/clear`.
4. New session: the user types **`continue`**. `CLAUDE.md` tells Claude to read
   `HANDOVER.md`, so it resumes exactly where it was.

## The rule in CLAUDE.md
```
## Session start
1. Read HANDOVER.md. Continue from there when the user says "continue".
2. Keep HANDOVER.md current after every commit and every subagent result.
   When the user says "handover", use the handover skill and stop.
```

## Writing good "how to resume" lines
Each in-flight item says what to do if the session died mid-way:
- "UNCOMMITTED: export button (files: ReportPage.tsx, export.ts). e2e was running;
  result unknown → rerun `npm run e2e`, commit if green."
- "Subagent was writing tests for parser.ts; if cut off: check `git diff`, rerun
  only the missing tests."

## Why `/clear` instead of one long session
A long session re-reads its whole history every turn: slower, costlier, and older
details get blurry. Two short files carry what matters. Clear early, clear often.

## Splitting out NEXT.md (optional)
One file is enough for most projects. Split when the **Next steps** list grows
past about 40 lines, or when several people plan work in the same folder:
- `NEXT.md` holds the plan: now, next, open questions, one line per task.
  It changes when plans change.
- `HANDOVER.md` keeps only the live state: State, In flight, Gotchas.
  It changes after every commit.
- `CLAUDE.md` session start becomes: "Read `NEXT.md`, then `HANDOVER.md`."

## Per-feature trackers (larger work)
For a feature spanning many sessions, keep a tracker doc (e.g.
`docs-claude/<feature>-build.md`) that opens with:
> A fresh session reads this file first and continues from **Next up**.

Sections: **Next up** (current state + numbered next steps), **Decisions**
("grilled with the user on YYYY-MM-DD: ..."), **History** (oldest first, with commit
and PR refs). Update it at every step boundary.
