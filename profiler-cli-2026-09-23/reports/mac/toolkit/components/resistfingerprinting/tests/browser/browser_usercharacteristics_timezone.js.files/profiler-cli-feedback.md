## `profile markers --search` times out on a large profile

- Command: `profiler-cli profile markers --session <s> --search user-characteristics` on a per-test mochitest profile whose parent main thread holds 3,008,407 markers (task B4bdVfUSSSy9A0fdw7zziA, profile_browser_usercharacteristics_timezone.js.json).
- Expected: the threads holding matching markers.
- Got: `Error: Timed out after 30000ms waiting for the daemon ... to answer.`
- Workaround: `zoom push` to the test's range, then `thread markers --search` on the one thread.

## Question: "the exact time of each marker in a filtered list"

- Command: `profiler-cli thread markers --category Test --search <test file> --list --limit 0`
- Needed: to order a Glean `String::set` (t=223.343 s) against the assertion reading it (t=223.881 s), both inside the same second.
- Got: times printed as `t=3m44s`, rounded to the second, so every event of the last second of the test shows the same time.
- Workaround: `--json` and a script printing `flatMarkers[].start`.
- What would have answered it: millisecond times in `--list` output (e.g. `3m43.343s`), at least when the listed markers span less than a few minutes.


## Question (review, again): "the start time of each marker, to the millisecond"

- Commands: `thread markers --search name:test --list --limit 0` on a resource-usage profile, and `--category Test --search <file>` on a per-test profile.
- Needed: millisecond start times, to compare the harness launch, the first test's start and one log line within about 2 s, and to check a report's quoted times.
- Got: times like `t=13m11s`. Workaround: `--json` and a script printing `flatMarkers[].start` on every list. This is the same issue as the entry above.
