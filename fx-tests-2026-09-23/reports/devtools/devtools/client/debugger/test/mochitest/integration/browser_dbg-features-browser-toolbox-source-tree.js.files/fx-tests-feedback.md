## Question: is this test still skipped anywhere, or was the skip-if already removed?

- Command: `fx-tests test devtools/client/debugger/test/mochitest/integration/browser_dbg-features-browser-toolbox-source-tree.js`
- Expected: SKIP rows under "Issues" to say the skip is no longer in effect, e.g. "last seen 2026-09-11", because it had not been seen in 10 days.
- Got: three `SKIP os == ...` rows ranked at the top of "Issues (first failure per run)", with no dates, which reads as a current skip. Only `--history` (skips drop to 0 from 2026-09-12) plus a `git log` of the manifest showed that central removed the skip-if on 2026-09-11.
- Workaround: `--history`, then `git log` on the manifest.

## Question: which tasks ran this test on a try push, so I can open one's resource-usage profile? (review-browser_dbg-features-browser-toolbox-source-tree.js)

- Command: `fx-tests try 2888bcab0070 --all-jobs --test devtools/client/debugger/test/mochitest/integration/browser_dbg-features-browser-toolbox-source-tree.js --task-ids` (also with `--profiles`, and with `--json`)
- Expected: under each config row, the task IDs (and with `--profiles`, the resource-usage profile URLs) of the job runs counted, passed ones included.
- Got: the same per-config count table in all three forms; `--task-ids` and `--profiles` are silently ignored with `--test`, and the JSON has only the counts.
- Workaround: Treeherder `api/project/try/push/?revision=` then `api/jobs/?push_id=` to map job names to task IDs.
