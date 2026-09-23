## Exact time of a marker past the first minute

- Command: `profiler-cli thread markers --session test_pluralForm-irish.js-1 --search pluralForm-irish --list --limit 0`
- Expected: `t=92.047s` for the `Timed out and was force-killed` ERROR marker, like `t=47.036s` for markers before 60 s.
- Got: `t=1m32s`, so the four markers of the log replay (92.015, 92.046, 92.047, 92.049 s) all read the same, and a 45.009 s duration can't be checked against start/end.
- Workaround: `--json` and read `flatMarkers[].start`. Keeping millisecond precision in the `m`/`s` form (`1m32.047s`) would have answered it.

## When a harness group (parallel / sequential / retry) started

- Command: `profiler-cli thread markers --session test_pluralForm-irish.js-2 --search name:parallel --list`
- Expected: the `parallel` group marker.
- Got: a test named `test_multipleOpensInParallel.js` (search is substring and case-insensitive; the group marker's name is `test` like every test marker).
- Workaround: `--search name:test --list --limit 0 --json` and a script keeping labels equal to `parallel`/`sequential`/`retry`. An exact-match form (`label:=parallel`) or a distinct marker name for groups would answer it.
