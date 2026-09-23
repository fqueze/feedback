## `fx-tests intermittent --bug 1921101` — which job and which device each occurrence was

- Command: `fx-tests intermittent --bug 1921101`
- Question: for each occurrence, which job (page) and which device (machine) did it run on? For Android hardware perf jobs, a device-farm problem shows up as a spread over devices.
- Got: the Occurrences table cuts the platform and job name to `android-hw-…` and `browsertime…` and has no machine column, even with `COLUMNS=300 --full-messages`. The "Job names" section does list the jobs, but not which task each one belongs to.
- Workaround: `--json`, then a script over `occurrenceRows[].{taskId,testSuite,machineName}`.
- Could show: one full job name and `machineName` per occurrence row. A "Machines" histogram next to "Platforms" would make a per-device cluster visible at a glance.

## `fx-tests task <id> --profiles` on a browsertime/raptor job

- Command: `fx-tests task LyTSWyBSTuWXzAv1Ecl7SQ --profiles`
- Expected: something that points at the job's failure, or at least says this harness is not covered.
- Got: `0 tests, 0 executions, 0 failing` and "This profile records no tests at all. Either the job is not a test job, or the harness died before it ran one". Both guesses are wrong: the job ran all 15 browsertime iterations to completion.
- Workaround: listed the task's artifacts through the Taskcluster queue API, then read `live_backing.log`, `logcat-*.log`, `*-browsertime.json` and the `N-original.mp4` videos by hand.
- Could show: recognise raptor/browsertime jobs (the name contains `browsertime-`) and say that per-test data does not apply to them. It could also list the per-iteration artifacts (browsertime.json, videos, logcat), and even the iterations whose visual metrics were 0.
