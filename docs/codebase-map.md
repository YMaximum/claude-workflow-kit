# Mapping the codebase

Goal: future sessions know what the app is and where things are without
re-exploring the code every time, while `CLAUDE.md` stays small.

## Where things go
| File | Holds | Size |
|---|---|---|
| `CLAUDE.md` (root, loaded every session) | app context (3-5 lines), key terms, commands, session start, "Always" rules, the **Read when relevant** routing table, top 3-5 gotchas | ≤ ~80 lines |
| `docs/codebase/README.md` | purpose and users, layout, entry points and data flow, area list, critical flows and no-go zones, external systems, direction and constraints | ≤ ~150 lines |
| `docs/codebase/glossary.md` | domain terms and their names in code | as needed |
| `docs/codebase/<area>.md` | one area: purpose, entry points, key files and symbols, how it works, patterns, tests, gotchas | ≤ ~150 lines |
| `docs/codebase/domain-rules.md` | business rules and invariants (e.g. "a slot is 15 minutes", "no double booking"), each marked enforced (where) or intended | as needed |
| `docs/claude/coding-style.md` | a long coding-style guide, if the project has one; root keeps at most 3 key rules | as needed |
| `<dir>/CLAUDE.md` (nested) | **only** hard local rules in folders where adding a file is fine | ≤ 5 lines |
| `docs/claude/*.md` | workflow details, agent routing, coaching | routed from root |

Claude Code loads a nested `CLAUDE.md` automatically when it works in that folder,
so use it only for rules that must never be missed. **Not** inside a no-touch
folder (frozen legacy, generated or vendored code): adding a file there is itself a
change. Put that rule in the root Gotchas instead, unless the user agrees otherwise.
Everything else is routed through the table, so it costs nothing until needed.

## 1. Survey (read-only)
- Tree: top two levels, ignoring `node_modules`, build output, vendored code.
- Manifests and config: package files, framework config, CI, Docker, env examples.
- Entry points: server start, CLI, routes, pages, background jobs.
- Data: models, schemas, migrations, external clients.
- Tests: where they are, how they run.
- Activity: which areas change most. Count per file, then group by eye into areas:
  `git log --since=6.months --format= --name-only | grep . | sort | uniq -c | sort -rn | head -25`
  (no recent history: use `--since` further back, or drop it).
- Existing docs: README, `docs/`, ADRs, comments that explain intent.

Small repo (under ~50 source files): read it directly. Larger: give one cheap
scout per top-level area, in parallel, this brief: "Map `<path>`: purpose, entry
points, key files/symbols, main flow, patterns, tests, gotchas. ≤40 lines. Cite
path and symbol for each claim. Say what you did not read." The main session
checks a few claims per report and writes the files.

## 2. Draft, then discuss the app context with the user
The code shows *how*; the user knows *why*. Draft an answer for each topic from
the code, then let the user confirm or correct:
1. **Purpose and users**: what it does, for whom, the main jobs.
2. **Domain terms**: business words and what they mean here (and in code).
3. **Critical flows and no-go zones**: what must never break; what not to touch.
4. **Direction and constraints**: legacy vs current, where it is heading,
   external systems, performance/hosting/compliance limits.

Grill style: show the drafts together and ask about them as one batch (they are
independent). If an answer opens a new question (e.g. "legacy" → "which folders
exactly?"), ask that follow-up next, not in the same batch.

## 3. Write
- `docs/codebase/README.md` and `glossary.md` from the drafts plus answers.
- Area files only for the **2-3 most active areas** (activity + the user's
  near-term work), from `docs/codebase/_AREA_TEMPLATE.md`.
- Every other area: listed in the README with "not mapped yet". The first ticket
  that touches it maps it in the same change and adds a routing row in `CLAUDE.md`.
- Root `CLAUDE.md`: 3-5 lines of app context, key terms, one routing row per
  mapped area, top gotchas. Nested `CLAUDE.md` only for hard local rules in
  folders where adding a file is fine (never inside a no-touch folder).
- Name symbols, not line numbers. Add "Last verified: <date>".

## Existing CLAUDE.md (review, then propose)
1. Split it into statements. For each: is it still true in the code? Check
   commands run, paths exist, named modules exist.
2. Show a table with **one action per row**: statement → **keep in root** /
   **fix** (what is wrong, how you checked) / **move to** `<file>` (too detailed for
   root) / **remove?** (only if clearly obsolete, and only with approval). A
   statement that needs two actions gets two rows.
3. Show the proposed new root **in full**, and list each moved block with its
   destination (paste moved content only if the user asks). Business rules go to
   `domain-rules.md`, long style guides to `docs/claude/coding-style.md`. Apply
   only what the user approves. Never delete without approval; when unsure, move
   rather than drop. Rules the code does not enforce are kept, marked "intended,
   not enforced", and reported.
4. Teammates read it too: summarise the restructure in the setup commit message.

## Keeping it true
- A change that adds, moves or removes a module updates its area file and the
  routing row in the same commit.
- Review checklist includes "is the map still true?"
- Claude fixes any wrong statement it finds while working, and says so.
- Update "Last verified" when you check a file against the code.
