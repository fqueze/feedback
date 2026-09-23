## Daemon dies silently loading a large profile

- Command: `PROFILER_CLI_SESSION_OWNER=test_crash_terminator.js profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/Z0J5s1oPSMyuBvjwAFAqhg/runs/0/artifacts/public/test_info/profile_shutdown_hang_26986.json" --session tct-4` (48 MB gzipped, several million markers), with `PROFILER_CLI_SESSION_DIR` set. Retried alone, with no other session loaded and `NODE_OPTIONS=--max-old-space-size=4500`: same result.
- Expected: the profile loads, or an error saying why not (out of memory, too many markers).
- Got: after about 6 s, `Error: Session tct-4 is not reachable. Nothing is accepting connections on .../tct-4.sock. The daemon exited without cleaning up.` The session log `tct-4.log` holds only the daemon's startup lines and `status` polls: no error, nothing naming memory.
- Workaround: none; I did without that profile. A sibling profile of the same kind (36 MB gzipped, 6.8 M markers) did load.

## Machine CPU over a time window in a resource-usage profile

- Question: "was the machine saturated while this test ran?"
- Command: `profiler-cli counter list --session tct-2` said `No counters in this profile.`; `profile info` said `No significant activity.` The CPU data is only in `CPU Use` markers (one per 100 ms, `cpuPercent` field), so I scripted over `thread markers --search 'name:CPU Use' --list --limit 0 --json` after a `zoom push` to bucket `cpuPercent`.
- What could have shown it: `profile info` or `thread markers` summarising `CPU Use` markers in the current zoom (mean / min / max of `cpuPercent`), or exposing them as a counter.

## Which marker a link's `marker=N` points to (review-test_crash_terminator.js)

- Question: "is marker index 1313696, from a report's link, the `Runnable DummyEvent` it quotes?" The thread holds 6.8 M markers, most of them identical `DummyEvent` / `AddTask` pairs, so `--search` cannot single it out.
- Command: `profiler-cli zoom push 5.0015,5.0025`, then `thread markers --list --limit 0 --json` (1048 rows), then `marker info <first/last handle> --json` to bracket the `markerIndex` values, then `marker info m-111 --json` to hit 1313696.
- What could have shown it: `marker info --index 1313696`, or `load <link>` printing the handle of the marker its `marker=` parameter selects.
