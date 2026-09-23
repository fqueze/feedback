# fx-tests feedback — browser_perf-01.js

## `fx-tests test` misses failing jobs on the culprit push

- Command: `fx-tests test devtools/server/tests/browser/browser_perf-01.js --task-ids --limit 0`
- Expected: every job in the window where the test failed. The regression was live on autoland
  pushes 277813–277824 (2026-09-03), and the culprit push d1eb98680c78 has two failing jobs
  (XK31QLALSl-8x3q9KxHtag, EckypvQ6TCaNT0IjWWyk3Q; 4 failures).
- Got: 2 failures in one job (PloQm6uxQ7GOqY93xBwYOQ, on 80c3d9ccdbe7), and the verdict
  "intermittent, fails on 1 configuration". `fx-tests try 80c3d9ccdbe7 --project autoland --test
  <path> --all-jobs` shows the test failed in **16 of 16** jobs on that same push (Linux, macOS,
  Windows; opt, debug, asan, tsan), plus 2 of 2 on d1eb98680c78. So `test` saw 1 of 18 failing
  jobs, and a one-hour permanent failure on every platform read as a 0.03% Linux intermittent.
  `fx-tests try d1eb98680c78 --project autoland` also says "0.0% on central (2/7584)", so the
  two views disagree about the same day.
- Workaround: found the culprit from the code history, then ran `fx-tests try <rev> --project
  autoland` on it.
- What would have helped: if backed-out pushes are excluded on purpose, say so in the `test`
  output ("N failing jobs on backed-out pushes not counted"), since those are the jobs that name
  the culprit.

## Question with no direct answer: "did this test pass on any push while the regression was live?"

- Needed: the test's outcome on each autoland push in a revision range (277813–277824).
- Did: one `fx-tests try <rev> --project autoland --test <path> --all-jobs` per push, each
  reading every test job's profile (slow, several minutes per push).
- Could have shown: `fx-tests test <path> --revisions <from>..<to>` (or `--push-range`) giving
  per-push pass/fail for the test.

## Review: `try --test` gives counts but no failure messages (review-browser_perf-01.js)

- Command: `fx-tests try 01a2a5bb2e63 --project autoland --test devtools/server/tests/browser/browser_perf-01.js --all-jobs`
- Expected: per-config counts plus the test's failure messages, to see whether this push failed
  the same way (JSON.parse at a 16384 boundary) as the push under review.
- Got: the counts table only (12 of 12 jobs failed), no messages.
- Workaround: a second, full-push run, `fx-tests try 01a2a5bb2e63 --project autoland --messages
  --task-ids --full-messages`, then grep for the test.
- Could have shown: with `--test`, the failure messages of that test under the table.
