# fx-tests feedback (browser_ext_identity_indication.js)

## `test --bugs` prints nothing when there is no annotated bug
- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_identity_indication.js --bugs` (also with `--progress`)
- Expected: a "Bugs" section, saying "no sheriff-annotated bug names this test" when empty.
- Got: the same output as without `--bugs`; no section at all. Only `--json` showed `"annotatedBugs": []`.
- Cost: could not tell "none" from "the live query silently failed" without a second, JSON run.

## Question: "in each failing job of my test, did the suspected leaker fail earlier in the same browser, and how?"
- Commands: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task <id> --json` for all 44 tasks and a Python script over `failures[]`.
- The default outputs could not answer it: `test --task-ids` lists jobs only, and `task` lists every failing test in path order, not in run order, so one job at a time and no "what failed before mine".
- What could show it: `test --task-ids` with, per job, the tests that failed before this one in the same manifest run (with their first message), or a `--cofailing` summary: "44/44 jobs also failed browser_ext_commands_execute_page_action.js (No anchor node ...)".

## The published data changed between two calls
- `fx-tests test <path>` at the start covered 2026-08-31..2026-09-20 (45 fails / 9,939 runs); later calls covered 2026-09-01..2026-09-21 (42 / 10,334), because a newer file was generated meanwhile.
- The "generated" line shows it, but nothing says the window moved since the previous call; counts copied from the first call no longer match later ones. A one-line stderr notice when the cached file is replaced would help.
