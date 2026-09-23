## `fx-tests task --profiles` lists a failure with no profile line and no reason

- Command: `fx-tests task Qh5u9KcBRKGV5DPnsXDE7Q.0 --profiles` (also `DaX4ba-cRBSfLyU1RkEaDg.0`), both test-linux2404-64-tsan/opt-xpcshell.
- Expected: either a per-test profile URL for the failing test_remote_tabs.js, or a note that none was uploaded, and why (for example "TSan jobs do not upload per-test profiles").
- Got: the FAIL entry and its message, with no `profile` line and no explanation. Only the resource-usage profile is listed for the job.
- Workaround: I picked non-TSan tasks, although TSan is the worst config. A note at the top of the output would have saved me two lookups.
