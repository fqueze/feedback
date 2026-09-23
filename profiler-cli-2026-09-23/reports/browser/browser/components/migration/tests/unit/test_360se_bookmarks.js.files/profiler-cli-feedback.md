## Question: "which of two tests started first, and by how many ms?" (resource-usage profile)

- Command: `profiler-cli marker info m-1 m-2 --session <id>` on a 23-minute resource-usage profile.
- Expected: start/end with millisecond precision, since the two `test` markers are 63 ms apart.
- Got: `Time: 4m17s - 4m19s (1.205s)` for both; `thread markers --list` also prints `t=4m17s` for both. The ms values are only in `--json` (`start: 257358.54`, `start: 257421.54`).
- Workaround: `marker info --json` and read `start`/`end`. The text output could keep ms (`4m17.359s`) when the rounding hides a difference between rows.

## Question: "how many TaskQueue::DispatchLocked markers fall between 657.81 and 662.52 ms?" (review, browser-review-test_360se_bookmarks.js)

- Command: `profiler-cli zoom push 657.81,662.52 --session <id>` on a 698 ms per-test profile, then `thread markers --search ... --list`.
- Expected: either ms understood (the profile is under 1 s and every time the CLI printed was in ms), or an error that the range lies outside the profile.
- Got: silently zoomed to 657.81 s–662.52 s ("View: ... (4.710s) | Full: 698.68ms"), and every query returned "No markers match".
- Workaround: `zoom push 0.65781,0.66252`. A range starting past the profile's end could be rejected, or accept an `ms` suffix like the printed times.
