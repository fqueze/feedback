# profiler-cli feedback (browser_ext_identity_indication.js)

## Question: "at what exact time, and in what order, did these markers happen?" (profile longer than a minute)
- Command: `profiler-cli thread markers --category Test --search identity_indication --list --limit 0 --session ...` (and `profile markers --search RefreshDriverTick`)
- Expected: millisecond times, as for profiles under a minute (t=9.631s).
- Got: every row at `t=2m1s` or `t=2m8s`; the order and spacing of the test's own steps (80 ms of test, then a 7.7 s wait) cannot be read.
- Workaround: `marker info ... --json` / `--list --json` and reading `start`. The list could print `t=121.519s` (or `2m1.519s`).

## Question: "which document are these RefreshDriverTick markers for?"
- Command: `profiler-cli thread markers --search "waiting for paint" --list --limit 0`
- Got: 6,903 rows with name and time only; the `innerWindowID` that ties them to the popup's `initial timer start` marker is only in `--json` `data`.
- What could show it: `--group-by field:innerWindowID` working on these payload-less markers, or the list printing innerWindowID (ideally resolved to the document URL from the matching page/timer marker).
