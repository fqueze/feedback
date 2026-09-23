## Question: "did this failure recur after the fix landed, or only on old revisions re-run later?"

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_webRequest.js --history` and `--task-ids --limit 0`
- Expected: a way to see the push date (or revision) of each failing run, since `--history` buckets by the day the job ran.
- Got: 3 failures on 2026-09-14, ten days after the fix, which read as a recurrence. They were jobs re-run on revisions pushed 2026-08-15. Finding that took `fx-tests task <id>` per task for its revision, then a Treeherder push API call per revision.
- Could show: the revision and push date next to each task ID in `--task-ids`, or a `--by-push-date` view in `--history`.

## Question: "which test failed first in this job?"

- Command: `fx-tests task QpSrFmkpR1Gt5mBA-g7xLw --messages`, also Xh6MnwM2R3OFkWAeuPuZLA
- Expected: failing tests in execution order, since the first failure in a manifest is usually the cause and the rest are fallout.
- Got: an order that is not execution order. In Xh6MnwM2R3OFkWAeuPuZLA, browser_ext_incognito_views.js (2 failing executions) is listed before browser_ext_commands_execute_page_action.js, which ran and failed first. Failures from other manifests (enterprisepolicies, customizableui, backup) also come first alphabetically. I had to load a profile and list the `test` markers to get the order.
- Could show: execution order within each manifest, or a "first failure in this manifest" flag.
