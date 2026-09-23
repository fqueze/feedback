## Question: in each failing job, did the suspected leaker fail first, and did this test's retry pass?

- Command: `fx-tests test <path> --task-ids --limit 0`, then `fx-tests task <id> --json` once per job (31 jobs), with a Python filter on `failures[].path`, `messages` and `passedOnRerun`.
- What would answer it: an option on `fx-tests test` listing, per failing job, the tests that failed earlier in the same job and whether this test's rerun passed (or a count of co-failing tests across the failing jobs).

## `fx-tests task --profiles` truncates the FAILED list without saying so

- Command: `fx-tests task QpSrFmkpR1Gt5mBA-g7xLw --profiles` (without `--limit 0`).
- Expected: either every failing test, or a "… N more (--limit 0 for all)" line as the guide promises.
- Got: `grep 'browser_ext_webrtc.js$'` on the output found nothing; with `--limit 0` the entry is there. (Possibly the truncation line was there and cut by my `head`; if so, ignore.)

## Correction to the entry above

- The truncation is announced: line 130 of the output reads "… 24 more (--limit 0 for all)". My `head` had cut it. Not a tool problem.
