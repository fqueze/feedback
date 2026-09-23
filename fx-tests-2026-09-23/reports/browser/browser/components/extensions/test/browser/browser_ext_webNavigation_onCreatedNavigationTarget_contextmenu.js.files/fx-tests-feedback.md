## Question: "did the same earlier test fail in every job where this one failed?" (browser_ext_webNavigation_onCreatedNavigationTarget_contextmenu.js)

- Command: `fx-tests test <path> --task-ids --limit 0`, then one `fx-tests task <id> --profiles --limit 0` per failing task (30 calls), grepping each for `browser_ext_commands_execute_page_action.js`.
- Expected: a way to see, for the failing jobs of one test, which other tests failed before it in the same job (a co-failure count: "browser_ext_commands_execute_page_action.js failed in 31/31 of these jobs").
- Got: only per-job listings, so a shell loop was needed. For a cascade victim this is the single most useful fact.

## Question: "are these failures on new pushes, or backfills of old ones?"

- Command: `fx-tests test <path> --history` / `--task-ids`
- Expected: the push date (or at least revision) next to each task, so that the day a task ran is not mistaken for the day the code was pushed.
- Got: tasks bucketed by run date only. The 3 failures on 2026-09-14 were backfills of 2026-08-15 autoland pushes, which made the test look like it still failed after the fix of 2026-09-04. Workaround: the Treeherder push API per revision (hg.mozilla.org json-pushes started returning HTTP 406 after a few calls).
