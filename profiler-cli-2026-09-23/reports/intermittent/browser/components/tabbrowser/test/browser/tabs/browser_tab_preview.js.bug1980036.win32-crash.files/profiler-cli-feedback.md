## Question: when exactly did this marker happen?

- Command: `profiler-cli marker info m-644 m-647 --session <s>` on a 19-minute resource-usage profile
- Expected: a timestamp precise enough to order and space markers (e.g. `906.037s`).
- Got: `Time: 15m6s (instant)` and `15m14s`, rounded to the second. The `--list` view rounds the same way.
- Workaround: `--json` and read `.start` in ms. Printing at least milliseconds (`15m6.037s`) would have answered it.

## Question: does this handle's marker have the index a profiler link names? (review-browser_tab_preview.js.bug1980036.win32-crash)

- Command: `profiler-cli marker info m-992 --session <s>`
- Expected: the marker's `markerIndex`, to match the `marker=N` of a link being checked.
- Got: no index in the text output; it is only in `--json`. Also, `marker info --json` has no `data` object for TestStatus markers (only `fields`), while `thread markers --json` `flatMarkers[]` do have `data`, so the same script cannot read both.
- Workaround: `--json` and `.markerIndex`, reading `fields[]` for the message. Printing the index in the text header (`Marker m-992 (#68131)`) would answer it.
