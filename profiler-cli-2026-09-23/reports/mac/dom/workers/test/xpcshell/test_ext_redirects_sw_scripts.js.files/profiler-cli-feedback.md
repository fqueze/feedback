## Question: "what was the machine's CPU use while this test ran?" (resource-usage profile)

- Command: `profiler-cli zoom push m-1 --session <s>` (the test's `test` marker), then
  `profiler-cli thread markers --search "name:CPU Use" --session <s>`
- Expected: some summary of the `CPU Use` markers' `cpuPercent` field over the zoomed range
  (mean, min, share of intervals at >=95%), since that is the whole point of those markers.
- Got: only the count and interval durations of the `CPU Use` markers; `counter list` says
  "No counters in this profile", and `profile info` says "No significant activity".
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` and a Python
  script averaging `data.cpuPercent`.
- What could have answered it: a per-field numeric summary for aggregated markers (or exposing
  the resource-usage CPU markers as a counter, so `counter info` works under zoom).

## Question: "when was this child process created, relative to the test's markers?"

- Command: `profiler-cli thread info --thread t-4 --session <s>`
- Expected: "Created at" in the same seconds-since-start form the marker lists use (t=3.984s).
- Got: `Created at: ts-8z` only — a handle, no time.
- Workaround: `--json`, which has `createdAt` in absolute ms, and subtracting the offset
  inferred from a `cpuActivity` entry's `startTime`/`startTimeStr` pair.

## A failed `load` leaves its session id taken

- Command: `profiler-cli load <taskcluster URL> --session trsw-2` failed with
  "Could not fetch the profile on remote server. Response was: 503 Service Unavailable."
  (transient; the artifact answered 303 to `curl -I` right after).
- Retrying the same command: "Session trsw-2 is already running. Stop it first or choose a
  different session id." — and then the same 503 error from the dead session.
- Expected: a load that fails leaves no session behind (or retries a 5xx once itself).
- Workaround: `profiler-cli stop trsw-2`, then load under a new id.

## (review) `zoom clear` leaves the samples commands on the last zoomed range

- Commands: `zoom push 1.770,6.600`, `thread samples-top-down --include-idle --json` (129
  samples), then `zoom clear` ("returned to full profile"), then the same samples command.
- Expected: the full profile's 667 samples; the header does say `View: Full profile`.
- Got: 129 samples again. After `zoom push 9.817,12.713` (92 samples) and `zoom clear`, 92
  again. `status` says `View range: Full profile` throughout. Markers commands are not affected.
- Workaround: `zoom push 0,<profile end>` instead of `zoom clear` before a full-range samples
  query.
- Cost: a sample count quoted as the whole profile's would have been the last zoom's.

## (review) Question: "how much of the time was this thread runnable but not running?"

- Command: `thread samples --include-idle` over the test, on a macOS CI profile.
- Expected: some statement of wall time, CPU time and waiting time for the thread, since a
  saturated machine shows as a thread that is awake but gets little CPU.
- Got: 667 "running samples" in 31 s at a 10 ms interval, with no Idle category at all on macOS
  (the waits show as `__psynch_cvwait`/`mach_msg2_trap` leaves in `Other`), and CPU in `thread
  info` as a bare percentage.
- Workaround: `thread markers --search name:Awake --list --limit 0 --json` and a script summing
  `duration` against `data["CPU Time"]`: awake 30.7 s, CPU 4.7 s.
- What could have answered it: `thread info` giving awake time and the CPU share while awake,
  and the ratio of samples taken to samples expected at the interval.

## (review) `thread markers --search "name:task" --list` never returned

- Command: `profiler-cli thread markers --session <s> --search "name:task" --list` on the
  parent main thread of dSzAANHA's per-test profile (1,306,863 markers), default limit.
- Expected: the four `task` markers in seconds, as `--category Test --list --limit 0` had
  given in under a minute on the same session.
- Got: no output after 15 minutes; later queries on the session were fine once it was killed.
  "name" is also the payload key of Text markers, so this probably matched every Text marker
  whose payload mentions "task".
- Workaround: read the `task` rows out of the `--category Test` list.
- Cost: 15 minutes, and a hung background command to find and kill.
