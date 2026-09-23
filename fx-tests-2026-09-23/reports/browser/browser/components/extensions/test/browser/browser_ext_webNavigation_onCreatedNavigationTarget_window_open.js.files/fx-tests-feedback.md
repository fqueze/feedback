# fx-tests feedback (browser_ext_webNavigation_onCreatedNavigationTarget_window_open.js)

## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_webNavigation_onCreatedNavigationTarget_window_open.js --bugs`
- Expected: a line saying which bugs name the test, or "no bug names this test".
- Got: output identical to the run without `--bugs`, so "no bug" and "flag ignored" look the same.
- Workaround: none; assumed no bug.

## Question: "in how many of this test's failing jobs did a given earlier test fail first?"

- Needed to confirm that `browser_ext_commands_execute_page_action.js` (the leaker) failed with `No anchor node` in every one of the 31 jobs where this test failed.
- Command: a shell loop of `fx-tests task <id> --limit 0` over the 31 task IDs from `fx-tests test <path> --task-ids --limit 0`, grepping each output.
- What could have shown it: `fx-tests test <path> --task-ids` listing, per failing job, the first test that failed earlier in the same job (the leaker suspect), or a `--also-failed <path>` filter.

## Question: "were all failing runs on pushes from before landing X?"

- `fx-tests test --task-ids` groups by the day the job ran, and `fx-tests task` gives the revision but not its push date. Here 3 jobs ran on 2026-09-14 against pushes from 2026-08-15, which makes `--history` look as if the failure came back after the fix.
- Workaround: Treeherder `api/project/<repo>/push/?revision=<rev>` per revision (hg.mozilla.org `json-rev` started answering HTTP 406 after a few requests).
- What could have shown it: the push date next to each task ID, or `--history` by push date.
