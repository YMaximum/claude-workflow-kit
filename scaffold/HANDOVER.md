# HANDOVER: the first file a fresh session reads (last update: YYYY-MM-DD)

Rule: keep this file current after every commit and every subagent result, so a
usage-limit cutoff or an interrupted session never strands work. When the user
says "handover", stop starting new work, record in-flight work here, and stop.
Keep it short (about 100 lines); move finished items to the ticket's progress log.

## State
- Branch `<branch>`, HEAD `<sha>`: <what is committed>.
- Uncommitted: <files / nothing>.

## In flight
- <task>: <files it touches>. If cut off: <how to resume, e.g. "check git diff, rerun tests, commit if green">.

## Next steps
One line per task, in priority order: `<ticket-id> <goal> — <model/owner> — <pointers>`.
1. 

## Waiting on the user
- <question or decision>

## Gotchas
- <things a fresh session would trip on: ports to free, slow commands, flaky tests>
