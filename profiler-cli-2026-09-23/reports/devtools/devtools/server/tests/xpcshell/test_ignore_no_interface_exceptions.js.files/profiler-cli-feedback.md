## Marker times past one minute are printed to the second

- Command: `profiler-cli thread markers --session test_ignore_no_interface_exceptions.js-1 --search test_ignore_no_interface_exceptions --list --limit 0` and `profiler-cli marker info m-1 m-3 ...`
- Expected: millisecond times, since the question was "how long after the first 0x80073CFC launch failure did this test start, and how long after its start did the harness give up".
- Got: `t=2m13s` for the test start, the `will retry` INFO and the first launch failure (`t=2m12s`), so the 36 ms gap and the 1.5 s gap are both invisible. `marker info` prints `Time: 2m13s - 2m58s (45.027s)`: the duration is precise, the start is not.
- Workaround: `--json` and read `flatMarkers[].start`.

## "What else was logged between these two markers" has no direct answer

- Question: does the replayed "full log" of this test hold anything besides the force-killed ERROR, i.e. did xpcshell print anything at all?
- Command: `profiler-cli zoom push 178.2375,178.2385` then `thread markers --list`.
- Got: 393 markers "in view", led by every long `test` interval overlapping that millisecond, and ~480 tests' Begin/ERROR/End lines share the same timestamp, so the list cannot say which lines sit between this test's Begin and End.
- Workaround: built links for the Begin, ERROR and End markers with profile-link.py and checked that their `marker=` indices are consecutive (12443/12444/12445). A `--after m-4 --limit 5` (markers following a handle in thread order), or showing the marker index in `--list`, would answer it.

## Outcome counts and start-time distribution of `test` markers needed a script

- Question: how many tests in this job were TIMEOUT, and how many of them started after the first launch failure (and over what span)?
- Command: `profiler-cli thread markers --search name:test --list --limit 0 --json | python3 ...` (Counter over the status prefix of the label, min/max of start for TIMEOUT markers after t=131.676 s).
- What would have shown it: `thread markers --search name:test --group-by field:status` with first/last start per group, or honouring a start-time zoom (markers starting in range, not overlapping it).
