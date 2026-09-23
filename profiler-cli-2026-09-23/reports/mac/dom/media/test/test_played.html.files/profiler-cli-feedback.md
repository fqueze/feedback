# profiler-cli feedback — test_played.html

## Mochitest `INFO` Log markers show "(empty)" in `--list`

- Command: `profiler-cli thread markers --session test_played.html-1 --category Test --list --limit 0` (zoomed on 216.2,216.8 of the per-test profile of task JGkYlVWySsy6nslfU9ndwA)
- Expected: each `INFO` row to show the test's `info()` text, e.g. `test8-big-short.wav-42: got ended`.
- Got: `[(empty)] INFO: (empty)` on every `INFO` row. `marker info m-32 --json` shows the payload is there (`fields[].value` = `"test8-big-short.wav-42: got ended"`), but its `formattedValue` is `"(empty)"` for both `level` and `message`, so the label built from it is empty too.
- Question it left unanswered: "what did the test log between two TEST-PASS lines" — the test's own log.
- Workaround: `marker info m-A..m-B --json` and a jq over `fields[].value`.

## Times in `--list` are rounded to the second on a long profile

- Command: same as above.
- Expected: millisecond times, since the rows of interest are within 50 ms of one another.
- Got: `t=3m36s` on every row; ordering within that second only from the row order.
- Workaround: `marker info --json` for `start`.

## `--list` shows no payload for markers without a table label (review-test_played.html)

- Question: "what `currentTime` did each `timeupdate` of this element report, and when".
- Command: `profiler-cli thread markers --session review-test_played.html-1 --search db50a285051747090 --list --limit 0` (per-test profile of task JGkYlVWySsy6nslfU9ndwA, zoomed on 216.250,216.400).
- Expected: `timeupdate` rows showing their payload (`currentTimeMs=78 mediaDurationMs=78`), as other media markers show `(flow=...)`.
- Got: `m-17 timeupdate t=3m36s instant ✗` with nothing after it; `HTMLMediaElement:Timeupdate` has a chart label but no table label, so the row is blank. Together with the whole-second `t=` column, the list could not answer the question at all.
- Workaround: `marker info m-1..m-25 --json` and a script over `start` and `fields[].value`.
- What would have answered it: fall back to `key=value` of the payload fields when a marker has no table label.
