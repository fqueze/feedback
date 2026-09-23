## Question: the sub-second order of the test's log lines against content-process events

- Command: `profiler-cli thread markers --category Test --search browser_test_element_picker --list --limit 0 --session <s>`
- Expected: each row's start time with ms precision (the race here is 2 ms wide: `Screenshots:OverlaySelection` at 83.7522 s vs `PBrowser::Msg_UpdateDimensions` at 83.7540 s).
- Got: `t=1m23s` / `t=1m24s` on every row of a 1m33s profile, so dozens of rows share one timestamp and nothing can be ordered against other threads. `marker info m-N` prints `Time: 1m24s (instant)` too.
- Workaround: `--json` and a Python one-liner printing `start/1000` with 4 decimals, for every list I needed (test log, DOMEvent list, cross-thread `profile markers`).
- Could have shown: ms precision in `--list` when the rows are closer together than the printed resolution (or always below 1 s spacing).

## `profile markers --search` does not match the text it prints as the marker label

- Command: `profiler-cli profile markers --search "resize - window" --limit 0 --session <s>` (also `"Screenshots:OverlaySelection - window"`, `"pointermove - div"`)
- Expected: the DOMEvent markers whose printed label is exactly `resize - window`.
- Got: no match. `--search "Screenshots:OverlaySelection"` alone matches.
- Workaround: search on one field value, then filter the label in Python over `--json`.
