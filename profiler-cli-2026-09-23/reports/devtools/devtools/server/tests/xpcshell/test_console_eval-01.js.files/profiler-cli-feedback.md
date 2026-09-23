## Question: "at what exact time did this marker start, to compare with another one 36 ms later?"
- Command: `profiler-cli marker info m-1 m-3 --session test_console_eval-01.js-1` (and `thread markers --list`) on a 3m37s resource-usage profile.
- Expected: start times precise enough to order markers that are milliseconds apart (e.g. `t=133.106s`).
- Got: `Time: 2m13s - 2m58s (45.038s)` and `t=2m13s` for both the test start and the `will retry` 36 ms later; the order and gap are invisible.
- Workaround: `thread markers --search ... --list --json` and reading `start` in ms.

## Question: "how many tests TIMEOUT'd, and did they all start after marker X?"
- Command: `profiler-cli thread markers --search name:test --list --limit 0 --json` then a Python script counting `label` starting with `TIMEOUT` and comparing `start` to the first `Error:-2147009284` marker.
- Expected: a way to group Test markers by `Status` field with start-time min/max per group (e.g. `--group-by field:status` showing first/last start), or a `--after <t>` / `--before <t>` filter.
- Got: only the JSON had it.
