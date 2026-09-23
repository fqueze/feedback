## When exactly did this marker start, to the millisecond?
- Command: `profiler-cli thread markers --search test_unsafeDereference --list --limit 0 --session <s>`
- Expected: a start time precise enough to compare with neighbouring markers (e.g. `t=133.572s`).
- Got: `t=2m14s` for every marker in a 3-minute profile; the 35 ms gap between the `test` marker and its `will retry` INFO, and the 1.9 s gap to the first launch failure, were invisible.
- Workaround: `--json` and read `.flatMarkers[].start`.

## How many markers of this kind start after time T, and where does mine rank?
- Command: `profiler-cli thread markers --search "name:test" --list --limit 0 --json` + a script filtering `label` startswith `TIMEOUT` and `start > T`.
- Could have shown: `--search` with a `-label`/status field filter plus a count and min/max start in the grouped (non-list) view; `thread markers --search "TIMEOUT —"` matched nothing although that text is in the listed label.
