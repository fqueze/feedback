## `--bugs` prints nothing, not even "no bugs found"

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_execute_page_action.js --bugs` (and the same for `browser_ext_webNavigation_onCreatedNavigationTarget_window_open.js`)
- Expected: the bugs naming the test. Bugzilla has bug 2072654 ("Intermittent browser/components/extensions/test/browser/browser_ext_commands_execute_page_action.js | single tracking bug", NEW) and bug 1378104 (older path `test-oop-extensions/...`, FIXED 2026-09-04) for the first.
- Got: the normal `test` output with no bugs section at all, so "no bug" and "lookup did not run" look the same.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=<test file name>'`.

## Question: "which test failed first in this job?" (the leaker suspect)

- Command: `fx-tests task <taskId> --messages --limit 0`
- Expected: failures in run order, or the first failing test marked.
- Got: failures listed alphabetically by path, so the first failure in run order is only found from the resource-usage profile. To check the same leaker across the 31 jobs I looped `fx-tests task` over every task and grepped for its message.
- What would have answered it: run order (or the manifest position) on each FAILED row, or a `--first-failure` summary per job.

## Question: "did these failures stop with a landing, or are the later ones on old revisions?"

- Command: `fx-tests test <path> --task-ids --limit 0`
- Expected: each failing task's revision with its push date or push id.
- Got: the date the job ran only. The three 2026-09-14 failures looked like a recurrence after the fix; they were jobs run on autoland pushes from 2026-08-15 (275396–275403), before the fix push 278044. Found by curling `json-pushes?changeset=<rev>` for all 31 revisions.
- What would have answered it: the push id/date next to each task id, or `--since-push <rev>` to count only runs on revisions after a landing.

## One failure mode split into dozens of Issues rows

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_execute_page_action.js`
- Expected: `uncaught rejection: PageActions: No anchor node for _<uuid>_` as one issue with its count.
- Got: 40+ rows of `1x`, one per extension UUID, which hides that it is the dominant mode.
- What would have answered it: normalizing UUIDs (and similar ids) before grouping messages.
