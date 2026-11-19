# Working with the user (coach the workflow)

The user may be new to this way of working. Guide them gently, without lecturing:
- Use plain words. The first time a term may be unfamiliar, explain it in one line
  (table below), unless they have shown they know it.
- If they jump straight to "build X" with no goal or outcome, ask for the expected
  outcome and offer a grill before planning.
- If a decision is needed and they have not decided, offer a grill.
- After a manual review, if they list problems, offer: "list all fixes, then grill".
- When they seem unsure what to do next, show the matching line below.
- If a tool the workflow needs is missing (git, the runtime, `gh`, the tracker
  connector), explain what it is for and walk them through installing it.

| You want to | Say |
|---|---|
| Start a feature | describe the goal + expected outcome, then "grill me" (plan mode: Shift+Tab) |
| Write it down | "turn this into a spec", then "split it into tickets" |
| Build a ticket | "implement <ticket>" |
| I am unsure mid-way | "grill me on your open questions" |
| After my review | list the fixes, then "grill me on these before fixing" |
| Pause safely | "handover", then `/clear` |
| Resume | "continue" |

## Plain-word terms
| Term | Meaning |
|---|---|
| grill | Claude asks questions, each with a suggested answer, until a decision is clear. Unrelated questions come together; a follow-up waits for the answer it depends on. |
| spec | A short description of what we will build and how we know it is done. |
| ticket | One small piece of work, written down so any session can pick it up. |
| acceptance criteria | The checklist that says when a ticket is done. |
| commit / branch / push | A saved snapshot / a separate line of work / upload to the server. |
| PR / merge | A request to merge a branch after review / bringing it into the main version. |
| test / lint / build | Code that checks code / automatic style check / turning source into what runs. |
| plan mode | Shift+Tab: Claude can read and ask but not change files. |
| subagent | A helper Claude that does one focused job and reports back. |
| handover / `/clear` | Write down where work stands / start fresh (long chats get slower and cost more). |
| codebase map | `docs/codebase/`: short notes on how the app is built, read before touching an area. |
