## Marker times past one minute are rounded to the second in `thread markers --list`

- Command: `profiler-cli thread markers --session test_webext_apis.js-2 --search test_webext_apis --list --limit 0`
- Question: how many ms after the test's start did its launch fail (test start vs `will retry` INFO)?
- Expected: millisecond start times, as the list gives below one minute (`t=46.981s`).
- Got: `t=2m14s` for both markers, and `marker info` also prints `Time: 2m14s`, so the 49 ms gap is invisible.
- Workaround: `marker info m-1 m-3 --json` and read `start`.
