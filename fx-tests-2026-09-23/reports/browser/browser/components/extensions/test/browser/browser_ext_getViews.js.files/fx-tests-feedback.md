## Question: which test in this job started the cascade (the leaker), across all failing jobs of a test

- Command: `fx-tests task <taskId> --json` for each of the 41 task IDs from `fx-tests test browser/components/extensions/test/browser/browser_ext_getViews.js --task-ids --limit 0`, then a Python script over the JSON to list, per job, which failing test had a message other than the vsync one.
- Expected: some way to see, for a victim test, what failed *before it in the same browser* in each failing job. For example `fx-tests test <path> --preceding-failures`, or the `task` FAILED list in run order with start times, so the first failure of the chain is visible.
- Got: the `task` FAILED list is not in run order (for some jobs `browser_ext_incognito_views.js` is listed before `browser_ext_commands_execute_page_action.js`), and nothing aggregates across jobs. I had to run 41 `task` calls and a script.
- Workaround: the script. It showed that every job had `uncaught rejection: PageActions: No anchor node` in browser_ext_commands_execute_page_action.js.

## `fx-tests test <path> --bugs` prints no bug section when there is none

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_getViews.js --bugs`
- Expected: a line like "Bugs: none name this test".
- Got: the same output as without `--bugs`. I could not tell whether the flag did anything.
