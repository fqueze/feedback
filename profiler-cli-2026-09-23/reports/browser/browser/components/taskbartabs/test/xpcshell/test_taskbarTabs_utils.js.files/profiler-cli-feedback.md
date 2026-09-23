## Marker list timestamps are rounded to the second on long profiles

- Question: how many milliseconds between the test's `exiting test` INFO marker and the `Failed NS_DispatchToMainThread() in shutdown` assertion on the IPC I/O thread, in a 23-minute xpcshell resource-usage profile.
- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli thread markers --session <s> --search taskbarTabs_utils --list --limit 0`
- Expected: start times precise enough to order and space markers within one test (ms), since a whole xpcshell test here lasts ~330 ms.
- Got: every marker of the test printed as `t=4m38s`, so the 46 ms gap (and the order of the image-decode warnings against the subtests) was invisible.
- Workaround: `--json` and a Python one-liner printing `flatMarkers[].start`.
- What the default output could show: millisecond precision (e.g. `t=4m38.459s`) when the markers listed span less than a few seconds, or a relative-time column.

## No CPU counters in a Windows xpcshell resource-usage profile

- Command: `profiler-cli counter list --session <s>` on `.../VLxs8lxRSnC4pPp0xRUL7Q/runs/0/artifacts/public/test_info/profile_resource-usage.json` (test-windows11-64-25h2/debug-xpcshell)
- Expected: the machine CPU track the brief describes, to check load when the failure hit.
- Got: `No counters in this profile.`; `profile info` says `No significant activity.` with one thread `mach` at 0 ms. Possibly a data gap on Windows xpcshell jobs rather than a tool bug; saying so ("this profile has no CPU data") in `profile info` would save the search.

## (review) How many tests were running at one instant

- Question: how many xpcshell tests were running in parallel while this test ran, in a resource-usage profile, to check a "about 15 tests at once" claim.
- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli thread markers --session <s> --search name:test --list --limit 0 --json`, then a Python script counting `test` markers whose [start, start+duration) covers a given time.
- Expected: a way to list the interval markers overlapping a time or a marker, e.g. `thread markers --name test --at 278.459` or `--overlapping m-1`.
- Got: the default list gives second-rounded starts and a duration but no end time, so I could not read concurrency from it. I did not try `zoom push`. The script gave 20 (the report said 15).
- What the default output could show: an `--at <t>` / `--overlapping <m>` filter for interval markers.

## (review) "has stack" marker with no stack

- Command: `profiler-cli marker stack m-12 --session <s>` on a `C++ warning` marker listed with ✓ (VLxs8lxRSnC4pPp0xRUL7Q resource-usage profile).
- Expected: a stack, or no ✓.
- Got: `[1] unknown!null`. These are log-derived markers in a resource-usage profile, so the stack is empty. The ✓ in `thread markers` sent me to look for one.
- Workaround: none needed; ignore the ✓ on these markers.
