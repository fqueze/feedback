## Question: "in which order did these markers, all in the same millisecond, happen?"

- Command: `profiler-cli thread markers --session S --thread t-0 --search DOMEvent --list --limit 0` (zoomed to 6 ms)
- Needed: whether the page's reflow came before or after `pointerdown`/`mousedown`, and whether the
  synthesized mousemove came before `mouseup`. The whole click is ~1.5 ms.
- Got: start times rounded to the millisecond (`t=44.986s` on a dozen rows), so the order within
  that millisecond was only the row order, and that order is not documented as start time (rows
  from different marker types interleave). `marker info` also prints `Time: 44.986s - 44.986s`.
- Workaround: `profiler-cli marker info m-296 m-351 ... --json` piped to a script to print
  `start`/`end` in ms with decimals. Side issue: `handle` came back as `None` in those JSON records,
  so I had to match them to the handles by position.
- What would have answered it: an option such as `--time-precision us`, or automatic extra
  decimals when the zoom range is under ~100 ms.
- Correction to the side issue above: the key is `markerHandle`, not `handle`; my script read the
  wrong key. Not a tool problem.

## (review) Question: "which handle is the marker a link's `marker=N` points at?"

- Command: `profiler-cli marker info m-423 m-455 m-457 --session S` (default output)
- Needed: each marker's `markerIndex`, to check a report's links. The default output does not
  print it, so every check goes through `--json`.
- Also: `marker info --json` returns a bare record for one handle but `{"markers": [...]}` for
  several, so the same script broke when I passed a single handle.
- What would have answered it: the index in the default `marker info` header, or
  `thread markers --index N`.
