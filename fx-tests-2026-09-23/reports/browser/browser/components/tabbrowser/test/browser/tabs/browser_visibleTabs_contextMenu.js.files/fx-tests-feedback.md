## Question: on which revisions did the failures happen? (step change inside one day)

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_visibleTabs_contextMenu.js --task-ids --limit 0` (and `--json`)
- Expected: the push revision of each failing task, so I could check that all 20 failures sit between a landing (14:04 UTC) and its backout (15:43 UTC) on the same day. `--history` is per-day, so a regression that lands and is backed out within one day shows as one red day.
- Got: task IDs, job names and days only; no revision in the text or in the JSON `taskIds` entries.
- Workaround: ran `fx-tests task <id> --profiles` 20 times (one per task, slow) and grepped the `autoland <rev>` line and the Treeherder URL for the full revision.
- Could have shown: a revision column (short hash plus push time) in `--task-ids`, or a `--by-revision` grouping.
