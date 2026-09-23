## Question: at what millisecond did each of these markers happen?

- Command: `profiler-cli thread markers --search DOMEvent --list --limit 0 --session <s>` (zoomed to a 2.3 s range, profile 9 min long)
- Expected: start times precise enough to order events a few ms apart (e.g. `t=228.575s`), since the zoom range is only seconds wide.
- Got: every row printed `t=3m48s` or `t=3m49s`; `marker info` likewise prints `Time: 3m49s`. The ordering of touch events 1-5 ms apart, and the 350 ms gap that was the whole diagnosis, were invisible.
- Workaround: `--json` and a python one-liner printing `start/1000` with 3 decimals, for every list I read (about 10 times).
- What would have answered it: seconds with ms precision in `--list` and `marker info` output, at least when the view range is under a minute.

## `marker info m-a m-b --json` shape differs from single-handle output

- Command: `profiler-cli marker info m-42 m-21 m-22 --session <s> --json`
- Expected: a list of records with the same keys as `marker info m-42 --json` (`markerHandle`, `start`, ...).
- Got: records without `markerHandle` (KeyError in my script); had to fall back to one call per handle.
- Workaround: loop over handles, one `marker info` each.
