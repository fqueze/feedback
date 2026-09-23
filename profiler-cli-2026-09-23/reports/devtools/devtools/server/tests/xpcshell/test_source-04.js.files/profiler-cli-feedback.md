# profiler-cli feedback (test_source-04.js)

## `zoom push` quietly accepts a range in "2m10s" form and zooms to 1 ns
- Command: `profiler-cli zoom push 2m10s,2m14s --session test_source-04.js-1`
- Expected: either a 4 s zoom (the list output prints times as `t=2m13s`, so that is the natural form to copy back) or an error.
- Got: `View: ts-1→ts-2 (1.000ns)`. The next marker query returned only the `install` phase marker, with no warning.
- Workaround: seconds, `zoom push 130,134`.

## Question: "what is the exact time of a marker after the first minute?"
- Command: `profiler-cli thread markers --search ... --list`
- Expected: millisecond timestamps, to tell whether two events 25 ms apart came in order.
- Got: `t=1m4s` for anything after 60 s, so `test_ext_background_early_shutdown.js` ending and the first launch failure both show as `t=1m4s`.
- Workaround: `marker info ... --json` and read `start`/`end` (its records also have no `handle` field, so matching them to handles meant relying on order).
- Could have shown: `t=63.871s` or `t=1m3.871s` in list output.
