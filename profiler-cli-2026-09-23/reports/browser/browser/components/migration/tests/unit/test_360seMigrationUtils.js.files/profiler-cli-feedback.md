## Question: was an instant marker inside another marker's interval, in a long profile?

- Command: `profiler-cli thread markers --session <s> --search 360se --category Tasks --list --limit 0` on a 23-minute resource-usage profile.
- Expected: start times precise enough to compare an ERROR marker with two `test` interval markers.
- Got: `t=4m44s` / `t=4m45s` (whole seconds once past a minute), so the overlap could not be read from the list.
- Workaround: `marker info m-21 m-22 m-72 --json` and a Python one-liner for `start`/`end` in ms. The list could keep millisecond precision (e.g. `4m44.096s`), or `--list` could accept `--within m-N`.
