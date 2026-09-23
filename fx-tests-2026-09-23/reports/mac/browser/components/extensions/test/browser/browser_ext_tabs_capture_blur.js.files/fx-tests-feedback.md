## `--bugs` is silent when no bug names the test

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_tabs_capture_blur.js --bugs`
- Expected: a line such as "Bugs: none name this test" (or the list).
- Got: exactly the output of the command without `--bugs`; nothing says whether bugs were searched and none found, or the flag was ignored.
- Workaround: took the absence as "none", unverified.

## Question: "which revision, and when was it pushed, for each failing job?"

- Deciding "no longer fails" needs each failing job's revision and push time, to tell a backfill of an old revision (the 3 failures on 2026-09-14) from a real recurrence. `fx-tests test --task-ids` lists task IDs by run date only.
- Workaround: one `fx-tests task <id>` per task (33 calls) for the revision, then one `json-pushes` per revision for the push date.
- What would answer it: the revision (and ideally its push date) next to each task ID in `--task-ids`, or grouping `--history` by push date rather than run date.
