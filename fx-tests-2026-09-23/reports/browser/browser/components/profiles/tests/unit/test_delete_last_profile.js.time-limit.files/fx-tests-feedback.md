## `fx-tests task` prints an hg revision that hg.mozilla.org no longer serves

- Command: `fx-tests task FTQHSQFERvWp1NGwhQTgpA --profiles` printed `autoland 4a8386ee966a`; `curl https://hg.mozilla.org/integration/autoland/raw-file/4a8386ee966a/<path>` returns HTTP 406 (so does `json-rev`).
- Expected: a revision the source can be read at.
- Workaround: Treeherder push API for the full hash, then `https://lando.moz.tools/api/hg2git/firefox/<hash>`, then `git show <git sha>:<path>`. Printing the git sha too would save three calls.

## Question: "did this job collapse, and from when" (a job where 546 tests timed out)

- Command: `fx-tests task NtysdyF7QsiHd9l9fBuHmg --profiles`
- Expected: that the job's timeouts are one event: e.g. "543 tests started between 58 s and 61 s all timed out 45 s later; 0 tests passed after 56 s".
- Got: the per-test list of 549 failures (130 KB of output), with the counts in the header only.
- Workaround: loaded the resource-usage profile and bucketed `test` markers by start time with a script. A one-line "N timeouts ending within X s of each other" summary would tell a job-wide stall from a test's own timeout at a glance.
