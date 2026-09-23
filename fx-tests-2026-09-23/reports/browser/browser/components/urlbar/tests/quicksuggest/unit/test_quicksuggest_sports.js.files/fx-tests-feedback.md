## Question: "which tests hit this crash signature in the same window?"

- Commands: `fx-tests failures --harness xpcshell --message "child process hang at shutdown" --tests` printed "No failure matched" because a crash signature is not a failure message. `fx-tests crashes --harness xpcshell --signature "child process hang at shutdown" --path browser/components/urlbar` printed "1,786 crashes, 28 tests" but has no `--tests` to name them.
- Expected: a way to list the tests behind one crash signature, the same way `failures --tests` does for messages. Alternatively, `failures --message` could say that the text matches a crash signature and point to `crashes`.
- Workaround: `fx-tests task <taskId> --limit 0` on one job, one job at a time.

## `task --profiles` on a CRASH row does not say that no per-test profile exists

- Command: `fx-tests task LEvE_LI7R_OKi-V60yZP_A.0 --profiles --limit 0`
- Expected: for the 54 "child process hang at shutdown" CRASH rows, a line saying that no per-test profile was uploaded, as it already shows the profile URL for FAIL rows.
- Got: no profile line for those rows, and nothing explaining why. To confirm that the artifact did not exist, I had to list the task's Taskcluster artifacts with curl.
