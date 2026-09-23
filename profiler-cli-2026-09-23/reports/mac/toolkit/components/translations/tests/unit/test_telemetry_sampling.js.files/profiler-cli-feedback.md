# profiler-cli feedback (test_telemetry_sampling.js)

## Question: how busy was the whole machine while one test ran (resource-usage profile)
- Commands: `profiler-cli zoom push m-1` (the test's `test` marker), then `thread markers --search "name:CPU Use" --list --limit 0 --json` and a script averaging `cpuPercent` / `idle_pct` per 10 s bucket.
- What would have answered it: the `counter info` style "over time" summary for the resource-usage `CPU Use` markers in the current zoom (mean CPU %, mean idle %, per bucket). `counter list` says "No counters in this profile", so the only machine-CPU data is these markers.
- Related question needing a script too: how many tests were running concurrently with this one (count of overlapping `test` markers over time).

## `profile info` and `thread info` disagree on a Windows thread's CPU time
- Session: profile_test_telemetry_sampling.js.json of task DlZ7eu3zTz2-CnwrFPAcEw (windows10-64 debug), thread t-0.
- `profile info`: `t-0: GeckoMain - 208394.241ms` (matches the sum of the raw `threadCPUDelta`, unit `ns`, which I computed from the JSON: 208.4 s over 241 s).
- `thread info`: `CPU activity over time: - 39% for 93922.0ms`.
- On a macOS profile the two agree (21980.805ms vs "18% for 22019.0ms"). Expected one figure, or the two labelled as different things. Cost: a detour through the raw JSON to find which one was the thread's CPU time.

## Default session directory not writable in this sandbox
- `profiler-cli load <url> --session X` failed with `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error message suggested `PROFILER_CLI_SESSION_DIR`, which worked. Minor; the message was good.

## (review) `--session` is rejected before the subcommand
- Command: `profiler-cli --session review-x-1 status`
- Expected: the global option to be accepted anywhere, like `load <url> --session X`.
- Got: `error: unknown option '--session' (Did you mean --version?)`. It only works after the subcommand (`profiler-cli status --session X`).
- Workaround: put `--session` last. Cost one round trip.

## (review) Question: the `test` markers of one test in a resource-usage profile
- Command: `thread markers --search "name:test,test_telemetry_sampling" --list --limit 0`
- Expected: that test's `test` markers only (first run, replay, retry).
- Got: the positive terms are OR'd, as documented, so this listed every `test` marker in the job: 818 KB of output.
- Workaround: `--search test_telemetry_sampling`, then grep for `test` rows. An AND form (for example `+term`), or a `--name` filter separate from `--search`, would answer it.
