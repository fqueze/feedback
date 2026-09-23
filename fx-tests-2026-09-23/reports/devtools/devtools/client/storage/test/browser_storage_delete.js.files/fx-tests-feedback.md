## Question: did this test run on the skip-if-removal try push, and on which configs?

- Command: `fx-tests try 2888bcab0070 --profiles --task-ids` (as the brief suggests for a skipped test)
- Expected: a line for the test, or a pointer that it passed.
- Got: 722 lines about other tests, nothing for this one; the answer needed `--test <path> --all-jobs` (slow, reads every job).
- Workaround: `fx-tests try <rev> --test <path> --all-jobs`. It would help if `--test` without `--all-jobs` said up front "0 failures in the failed jobs; rerun with --all-jobs for pass counts", which it does, but the default `try` output could also name which skip-if conditions the push exercised: here the removed skip-if was `debug && http3` on linux, and no http3 config ran at all, which I had to infer from the config names.
