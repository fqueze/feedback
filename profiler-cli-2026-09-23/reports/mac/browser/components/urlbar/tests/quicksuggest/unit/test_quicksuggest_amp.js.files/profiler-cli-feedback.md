## Question: "how busy was the whole machine during this test's run?" (resource-usage profile)

- Command: `profiler-cli zoom push m-1` (the `test` marker of the timed-out run), then
  `profiler-cli thread markers --search "name:CPU Use" --list --limit 0`
- Expected: the CPU percent per row, or a summary (mean / median / share of time above 95 %) of the
  `CPU Use` markers in the zoomed range.
- Got: 318 rows of `CPU Use  t=4m48s  88ms`, without the value; each needs `marker info` to read
  "CPU Percent: 99.3%".
- Workaround: `--json`, then a python script averaging `data.cpuPercent` over `flatMarkers`.
  `counter list` says "No counters in this profile", so `counter info` over the zoom (which would
  have answered this directly) is not available for resource-usage profiles.

## Question: "how many other tests were running concurrently with this one?" (resource-usage profile)

- Command: `profiler-cli thread markers --search "name:test" --category Tasks --list --limit 0 --json`
  and a script counting `test` markers of type `Test` covering each second of the failing run.
- Expected: something like a concurrency count for a zoomed range, or per-marker overlap count.
- Got: nothing in the default output answers it.

## Default session directory not writable in the sandbox

- Command: `profiler-cli load <url> --session test_quicksuggest_amp.js-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a clear hint to set
  `PROFILER_CLI_SESSION_DIR`. Cost one round trip; the message was good. `profile-link.py` needed the
  same variable.

## (review) `zoom clear` / `zoom pop` leave `thread samples` on the last pushed range

- Commands (profiler-cli 0.9.0, session on T7GBmmEpRdWf's `profile_test_quicksuggest_amp.js.json`):
  `zoom push 30.3,30.482`, `zoom clear`, then `thread samples --include-idle --json`.
- Expected: 2,850 samples, the full profile, as `status` ("View range: Full profile") and the
  header say.
- Got: 14 samples (the 30.3–30.482 s range), `currentViewRange: null`. `zoom push 0,30.4` then
  `zoom pop` likewise gives 2,846. Markers and counters were not affected; samples were.
- Workaround: `zoom push 0,<profile end>` before any samples query. Cost: one wrong reading of
  "7 running samples" for a 30 s profile, and a round of checking.

## (review) `thread markers --search "name:task" --list --limit 0` does not return on a 1.9M-marker thread

- Question: "how long did each `add_task` take?" (the `task` interval markers)
- Command: `thread markers --session <s> --search "name:task" --list --limit 0` on the GeckoMain of
  TOOEp2dYTCy6's `profile_test_quicksuggest_amp.js.json` (macOS xpcshell: 1,923,221 markers, mostly
  `DummyEvent` runnables and `TaskController::AddTask`, ~42k per second).
- Got: no output after 12 minutes; the client could not be killed from the sandbox. `--category Test`
  plus `--search` answered in seconds.
- Would help: a warning or a time limit for an uncategorized search over a thread that large, or
  a per-task summary for xpcshell (name, start, duration) from the `task` markers.

## (review) Question: "how long did the test wait between each `X Started` / `X Done` log pair?"

- Command: `thread markers --category Test --search "Syncing Rust backend,Done syncing Rust backend,Starting" --list --limit 0 --json`,
  then a script pairing "forceSync Syncing Rust backend" with the next "Done syncing Rust backend"
  and summing per task.
- Expected: some way to get paired-log-line durations; the default output lists instants only.
- Got: nothing in the default output answers it. On the linux debug profile these waits were
  35.1 s of 61 s, the main finding of the review.
