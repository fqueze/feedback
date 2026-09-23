## `fx-tests try` undercounts a test's failures when jobs hit their max run time

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids --limit 0`
- Question: on which jobs of the push did `test_arrowpanel.xhtml` fail?
- Expected: all 8 `mochitest-chrome` (non-gpu) jobs on macosx1500-aarch64, opt and debug — Treeherder's error summary lists `Test timed out` for the test in every one of them.
- Got: "6 failures in 7 runs, across 4 job runs on 2 configs", naming only the two debug chunks, "1 run not read". The four opt jobs (`opt-mochitest-chrome-1proc` x2, `opt-mochitest-chrome-no-nv` x2) and the second `debug-mochitest-chrome-1proc-1` are absent from the entry; the only hint is a stderr warning with `--all-jobs` ("5 of 236 jobs were killed for exceeding their maximum duration ... failures in those jobs are not in this report"). "2 configs" is also two chunks of one config.
- Cost: a reader concludes the test passes on opt. I had to go to `treeherder-cli <rev> --json --filter chrome --match-filter all`, then the Treeherder jobs API to map job ids to task ids.
- What would have answered it: in the per-test entry, list the jobs that ran the test but were not read (killed jobs whose Treeherder error summary names the test), with their task ids, and count configs by platform/build type rather than by chunk.
