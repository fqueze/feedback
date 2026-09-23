# fx-tests feedback (browser_ext_runtime_setUninstallURL.js)

## `--task-ids` job name disagrees with the task's own name

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_runtime_setUninstallURL.js --task-ids --limit 0`
- Expected: `IT0S_eCQRJ-SkMnxZ3h6jA.0  test-macosx1500-aarch64/opt-mochitest-browser-chrome-7`, as `fx-tests task IT0S_eCQRJ-SkMnxZ3h6jA.0` and the Taskcluster task metadata both say.
- Got: `...opt-mochitest-browser-chrome-1` (same for `VYlSTop-RXKVXW_jxzuPVw.0`).
- Workaround: read `metadata.name` from `https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/<id>`.

## Question: do the revisions of these failing runs contain a given fix?

- The question: "did any of this test's failures run on a revision that has bug 1378104's fix?"
- Commands: `fx-tests task <id>` for each of the 36 tasks, to get the revision. Line 2 has only a 12-character hg hash, and the Lando `hg2git` API needs the full one, so I grepped `revision=` out of the Treeherder URL. Then `hg2git` per revision and `git merge-base --is-ancestor <fix> <rev>` locally.
- What the output could have shown: the full revision and its push time next to each task ID in `--task-ids`. `--history` and `--task-ids` group runs by the day the job ran, so the three 2026-09-14 failures look like failures after the fix. They ran revisions pushed on 2026-08-15.

## Question: in which jobs did the leaker fail but this test pass, and what reset the browser in between?

- The question: for a victim test, which of the leaker's failing jobs did not fail this test, and why (a timeout or crash that restarted the browser between them, or a `-standalone` config).
- Commands: `comm` of the two `--task-ids` lists. Then, for each of the 16 leftover jobs, `fx-tests task <id> --passed --limit 0 | grep` for this test's outcome and for the `TIMEOUT`/`CRASH` rows.
- What the output could have shown: per job, the tests that `TIMEOUT` or `CRASH` in the same manifest run, next to this test's outcome.
