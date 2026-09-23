## Question: "what wall-clock (epoch ms) time was this marker at?" — to compare with Date.now() values the product logs

- Command: `profiler-cli marker info m-725 --session <s> --json | python3 -c '... 1790014241951.315 + r["start"] ...'` (startTime taken from `profile meta --json`)
- Expected: `marker info` (and ideally `thread markers --list`) able to show the absolute time, e.g. `Time: 948.996ms (epoch 1790014242900.31)`, since product logs (Log.sys.mjs console messages, `installDate`, `selectedDate`) carry `Date.now()` values.
- Got: only profile-relative time, printed at 3 significant digits in text mode for times in seconds (`3.322s`), so sub-ms placement needed `--json` plus a script adding `meta.startTime`.
- Workaround: the script above, one `marker info --json` call per handle.

## `marker info` with several handles: `--json` shape differs from the single-handle one

- Command: `profiler-cli marker info m-659 m-661 m-725 --session <s> --json`
- Expected: a list of the same records a single-handle call returns (with `start`, `name`, `fields`).
- Got: an object whose shape my `r.get('start')` script did not match (single-handle call returns a flat record); cost a retry.
- Workaround: one call per handle.
