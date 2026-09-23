## `task --profiles` does not list the hang profiles captured on a timeout

- Command: `fx-tests task eQt7Hs7TSpWl5VNXPAPeEw --profiles` (also `Tgt7xxyeQceFVY-w55nuZA`, `R26giHXuRGS0E65CEvZNuw`)
- Expected: under each `application timed out after 370.0 seconds with no output` test, the profile the harness captured from the hung browser. On macOS/Linux the harness sends SIGUSR1/SIGUSR2 to the hung pid and uploads `public/test_info/profile_0_<pid>.json`; the log line `profiler Sending SIGUSR1 to pid <pid> start the profiler.` right after the TEST-UNEXPECTED-TIMEOUT maps the pid to the test.
- Got: no profile line for the timed-out tests, so it reads as "no profile exists for this failure".
- Workaround: listed the task's artifacts from the Taskcluster API, then grepped live_backing.log for `Sending SIGUSR1 to pid` to match `profile_0_4232.json` to this test.
