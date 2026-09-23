## Question: which other tests fail in the same jobs as this one, across all its failures?

- Command: `fx-tests test toolkit/components/glean/tests/browser/browser_fog_gmp.js --task-ids --limit 0`, then `fx-tests task <id>` once per task (36 calls, a few minutes).
- Expected: a co-failure summary on `fx-tests test` (e.g. "fails together with browser_fog_gpu.js, browser_fog_rdd.js, ... in 36/36 of its failing jobs").
- Got: only per-task lists; I had to loop over 36 tasks and grep.
- Why it matters: here the whole manifest after its first test fails together in every job, which points at session state rather than the test itself. That was the key clue and cost 36 calls to confirm.

## `fx-tests task`: the rerun outcome is only stated for some jobs

- Command: `fx-tests task S5D2POUNS7Sch2PsbMUHwA` (and 28 others).
- Expected: every "1 failing execution of 2" row says what the rerun did.
- Got: 7 of 36 jobs print "Passed when the harness reran it."; the other 29 print "FAIL — 1 failing execution of 2" with nothing about the rerun, so I cannot tell whether it passed or wasn't recorded.

## `fx-tests test --bugs` prints no bug section at all

- Command: `fx-tests test toolkit/components/glean/tests/browser/browser_fog_gmp.js --bugs`
- Expected: a "Bugs" section, even if it only says "none open", or a list including RESOLVED ones (bug 1790454 "Intermittent toolkit/components/glean/tests/browser/browser_fog_gmp.js | single tracking bug" exists, RESOLVED).
- Got: the same output as without `--bugs`, so I couldn't tell whether the search ran. Workaround: Bugzilla REST `short_desc` search.
