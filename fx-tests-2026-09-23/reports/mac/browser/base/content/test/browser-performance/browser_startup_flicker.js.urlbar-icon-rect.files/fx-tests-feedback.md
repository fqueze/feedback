## Question: "which failing runs of this test contain a given (not first) failure message, per config"

- Command: `fx-tests failures --harness mochitest --message "w:16, h:16" --path browser/base/content/test --tests` gave 0 rows for browser_startup_flicker.js, because it only sees the first failure message per run (a `todo()` there, see the sibling report's feedback). I then ran `fx-tests task <id> --messages --full-messages` for each of the 91 task IDs from `fx-tests test <path> --task-ids --limit 0`, on both paths, and grepped.
- Expected: `fx-tests test <path> --message <substring>` (or `--issue` over all messages) giving the count of runs containing that message, per config, with their task IDs.
- Got: nothing at that granularity without the 91-call loop (fast, about 2 s each, but it has to be scripted).
- What would have answered it: a per-run "all messages" view in `fx-tests test`, filterable by substring.
