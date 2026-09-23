## Precise start times of markers past 60 s

- Question: when exactly (ms) did this test start, relative to the first launch failure, in a 2-3 min resource-usage profile?
- Command: `profiler-cli thread markers --search test_objectgrips-07 --list --limit 0 --session <id>`
- Expected: `t=65.515s`-style times, as shown for markers under 60 s.
- Got: `t=1m6s` for the test marker, `t=1m4s` for the launch failure, so the 1.6 s gap and the 29 ms "will retry" delay were unreadable.
- Workaround: `profiler-cli marker info m-N --json | python3 -c '...d["start"]'` once per marker.
- What the output could show: millisecond precision in `--list` regardless of magnitude (e.g. `t=65.515s` or `t=1m05.515s`).

## Searching the text a list row shows

- Command: `profiler-cli thread markers --search "PASS —" --list` (to list passing `test` markers in a zoom)
- Expected: the rows displayed as `PASS — <test>`.
- Got: no match; the row text is composed from the `status` field and the test name, so it is not searchable as shown.
- Workaround: `--search status:PASS`, after reading the field keys with `marker info --json`.
