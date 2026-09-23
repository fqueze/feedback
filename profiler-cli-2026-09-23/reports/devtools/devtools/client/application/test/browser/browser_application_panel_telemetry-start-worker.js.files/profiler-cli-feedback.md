## `✓ has stack` on log-derived markers whose stack is empty

- Command: `profiler-cli thread markers --session <s> --search telemetry-start-worker --list --limit 0` on a resource-usage profile (task L64SbDEdT6eQ_tG_xy7XyQ), then `profiler-cli marker stack m-63 --session <s>`
- Expected: `✓` only for markers with a usable stack.
- Got: every `C++ warning` marker is flagged `✓`, and `marker stack` prints a single frame `[1] unknown!null`.
- Workaround: ignored the flag; read process/thread from `marker info` fields.

## `marker info --json` and `thread markers --json` use different shapes

- Command: `profiler-cli marker info m-65 --session <s> --json`
- Expected: the same marker record as in `thread markers --list --json` `flatMarkers[]` (with `label`).
- Got: no `label` key, so a script written against `flatMarkers` failed with `KeyError: 'label'`.
- Workaround: read the saved `flatMarkers` JSON instead.

## `marker info --json` changes shape with the number of handles (review)

- Command: `profiler-cli marker info m-19 m-20 ... --json --session <s>` versus `profiler-cli marker info m-39 --json --session <s>`
- Expected: one shape, e.g. always `{markers: [...]}`, so one script handles both.
- Got: several handles give `{type, requested, markers, errors, context}`; a single handle gives a bare record, so `['markers']` raised `KeyError`.
- Workaround: none needed for the review; would have had to branch on the shape.
