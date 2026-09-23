## Question: the exact time of a marker (ms precision)

- Command: `profiler-cli marker info m-15 m-17 m-16 --session browser_browser_toolbox_rtl.js-1` (and `thread markers --list`)
- Expected: a start time precise enough to order markers a few ms apart and to align with log timestamps (e.g. `195.604s`).
- Got: `Time: 3m16s` for markers 13 ms apart (`Browser toolbox process closed` and the `Got 1` failure); the list view shows the same rounding.
- Workaround: `thread markers --list --json` and read `start` in ms. The text output could print seconds with 3 decimals, as `profile-link.py`'s ranges need anyway.
