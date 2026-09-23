## Question: "which JS function caused each of these markers" (SetNeedStyleFlush over a 230 ms window)

- Command: `profiler-cli thread markers --search name:SetNeedStyleFlush --list --limit 0 --session S`, then `marker info <69 handles> --json`
- Expected: a list mode that shows, per marker, its innermost JS frames (or the stack's leaf JS function) on the same row, so a timeline of "who dirtied style when" reads without one `marker stack` call per marker.
- Got: the list shows only `✓` for "has stack"; the stacks need `marker stack` per handle or `marker info --json` for all of them.
- Workaround: `marker info <handles> --json` and a Python script printing the first 5 frames with `!` in `nameWithLibrary`. Something like `thread markers --list --stack-summary 5` would have answered it directly.

## `marker info --json` changes shape with the number of handles (review-browser_markup_load_01.js)

- Command: `profiler-cli marker info m-9 --json` vs `profiler-cli marker info m-41 m-52 --json`
- Expected: one shape, so a single script checks `markerIndex` for one link or several.
- Got: one handle gives a bare marker object; several give `{type, requested, markers: [...], errors, context}`. My script broke twice.
- Workaround: `d.get('markers', [d])`.
