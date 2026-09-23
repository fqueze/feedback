## Question: which process and which test emitted the assertions inside this test's range? (review-browser_animation_logic_overflowed_delay_end-delay.js)

- Command: `profiler-cli zoom push m-1 --session review-overflowed-2` then `profiler-cli thread markers --session review-overflowed-2 --search 'name:C++ assertion' --list --limit 0`
- Expected: enough per row to tell whether the 208 assertions in the range came from the test's own content process, or group them by `pid` / `test` field.
- Got: each row shows only message and file:line; the `pid` and `test` payload fields are hidden (I did not try `--group-by field:pid,field:test`, which the guide's `field:eventType` example suggests may exist). The resource-usage profile tags output with the harness's current test, which lags (here 3 to 21 s), so rows inside a test's range are often another test's.
- Workaround: `--json`, then a Python script grouping `flatMarkers[].fields` by (message, pid, test).
- Could have shown: the `test` field (and `pid` for cppDebug markers) in the `--list` row when it differs from the test whose range is zoomed, or a hint to use `--group-by field:test`.

## `zoom push` takes seconds, `marker info --json` gives milliseconds

- Command: `profiler-cli zoom push 755000,762000` after reading `"start": 759909.284` from `marker info --json`
- Expected: an error for a range far past the profile end (23 m).
- Got: silently accepted, view `ts>104 to ts>105`, then empty results.
- Workaround: `zoom push m-<N>`.
