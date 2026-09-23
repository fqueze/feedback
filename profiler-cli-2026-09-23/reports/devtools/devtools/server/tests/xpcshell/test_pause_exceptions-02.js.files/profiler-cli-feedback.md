## Exact times and "which tests timed out together" needed a script over `--json`

- Question: when exactly did this test's `test` marker start, and did its launch fail? Plus: how many other tests started TIMEOUT markers in the same few seconds, and which were its neighbours in start order?
- Command: `profiler-cli thread markers --search test_pause_exceptions-02 --list --limit 0 --session <s>`
- Expected: millisecond start times, since the gaps that matter here are 32-42 ms (test start to `will retry`).
- Got: `t=2m13s` for both the test start and `will retry`, and `t=1m32s` for all four replay markers, so their order and gap could not be read. Counting the 1057 TIMEOUT `test` markers that started in a 5 s window meant a Python script over `--json` (`flatMarkers[].start`, `label`).
- Would have answered it: ms precision in `--list` once a value is over 60 s (e.g. `t=133.368s`), and a `--group-by` or `--count` for `test` markers by status within a zoom.
- Workaround: `--json` piped to python.
