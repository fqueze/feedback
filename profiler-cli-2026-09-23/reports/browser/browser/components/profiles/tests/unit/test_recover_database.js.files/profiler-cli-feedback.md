## `zoom push` silently misreads the time format profiler-cli itself prints

- Command: `profiler-cli zoom push 13m31s,13m47s --session browser-test_recover_database.js-1`
  (times copied from `thread markers --list`, which prints `t=13m31s`).
- Expected: a zoom on 811 s–827 s, or an error saying only seconds are accepted.
- Got: `Pushed view range: ts-1 (13s) to ts-1 (13s) (duration: 0s)` — parsed as 13 s, a zero-length
  range, and every following query silently ran on it (one CPU marker instead of ~150).
- Workaround: convert by hand to seconds, `zoom push 808,828`.

## Question: "was the machine saturated during this window?" on a resource-usage profile

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` inside a zoom,
  then a Python script averaging `data.cpuPercent` per second / per minute.
- `counter list` says "No counters in this profile" on the resource-usage profile, and
  `profile info` says "CPU activity over time: No significant activity", although the profile
  carries a `CPU Use` marker every ~130 ms with the machine-wide CPU percentage. Either of them
  showing those markers as a CPU-over-time series (per zoom) would have answered it directly.

## Question: "which tests' child processes hung at shutdown in this job, and when"

- Command: `thread markers --search "hanging at shutdown; attempting" --list --limit 0 --json`, then
  a script to print each marker's `test` field next to its start time.
- The default `--list` row shows the message but not the marker's `Test` field, which is what
  says which test the line belongs to in a parallel xpcshell job. A `--group-by field:test` (or the
  test column in the list) would have answered it.
