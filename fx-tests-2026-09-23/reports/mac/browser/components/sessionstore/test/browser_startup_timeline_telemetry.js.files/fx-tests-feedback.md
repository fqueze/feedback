## `fx-tests task --profiles` does not list the hang profile taken on an application timeout

- Command: `fx-tests task ETAtj63lR0aIgKZp9Vk0Bw --profiles`
- Expected: for `browser_startup_timeline_telemetry.js`, `TIMEOUT — application timed out after 370.0 seconds with no output`, a `profile` line pointing at the profile the harness captured for the hang (the log says `profiler Sending SIGUSR1 to pid 5934 start the profiler` ... `Successfully moved profile_0_5934.json`, uploaded as `public/test_info/profile_0_5934.json`).
- Got: no profile line under that test at all, so it reads as "no profile exists".
- Workaround: listed the task's artifacts through the Taskcluster API and grepped `live_backing.log` for `profile_`; the hang profile is `profile_<n>_<pid>.json`, not `profile_<test>.json`.
- The question it should answer: "what was the browser doing when the harness killed it for no output" — the one profile that answers it for every `application timed out` failure.

