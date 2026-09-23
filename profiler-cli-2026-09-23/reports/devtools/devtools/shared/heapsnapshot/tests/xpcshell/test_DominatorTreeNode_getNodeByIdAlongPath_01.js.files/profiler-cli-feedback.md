## Precise marker timestamps for aligning with an external log

- Question: at what exact profile time (ms) did these harness log markers fire, to align them with the device clock of the task's logcat?
- Command: `profiler-cli thread markers --session <s> --search getNodeByIdAlongPath --list --limit 0`
- Expected: start times with ms resolution (e.g. `t=178.114s`), since markers seconds apart are the whole point of the comparison.
- Got: `t=2m58s` style times rounded to the second, for every row of a 25-minute profile.
- Workaround: `profiler-cli marker info m-N --json` per marker in a loop, reading `.start`.

## (review) Which tests were running at a given moment

- Question: which `test` interval markers were running at t=177.9 s (when the failing test was launched), and which ran for over 60 s?
- Command: `profiler-cli thread markers --session <s> --search test --list --limit 0 --json`, then a Python script over `flatMarkers` checking `start < t < start + duration`.
- Expected: a way to ask for interval markers overlapping a time, e.g. `--at 177.9` or `--overlapping 175,180`, and a `--min-duration`; `zoom push` then `--list` would do it if zoom kept markers that start before the range.
- Got: only a flat list of 1,625 `test` markers with second-rounded start times.
- Workaround: script over the JSON.

## (review) Count of test markers by status

- Question: how many tests in this job are TIMEOUT / PASS / FAIL?
- Command: same `--json` dump, then a `collections.Counter` over the label's first word.
- Expected: `thread markers --search test` summary grouped by the marker's `status` field (it already groups by type and category).
- Workaround: script over the JSON.
