## Question: in what order did these markers happen, to the millisecond?

- Command: `profiler-cli thread markers --search placesContext --list` and `profiler-cli marker info m-135 m-136 m-146 --session ...`
- Expected: start times precise enough to order events that are ms apart (e.g. `t=78.6668s`).
- Got: every row and every `marker info` record says `t=1m19s` / `Time: 1m19s` — once past 60 s, times are rounded to the second, so popuphiding / deactivate / command within 7 ms are indistinguishable and the list order is the only clue.
- Workaround: `marker info <m> --json` one handle at a time, piped through python to print `start`. (Multi-handle `--json` output is several concatenated JSON documents, not one array, so `json.load` fails on it.)
- Could show: ms (or µs) precision in `--list` rows and `marker info` when the view is under a few seconds, or always `1m18.6668s`.
