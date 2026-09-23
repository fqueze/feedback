## Question: in what order, to the millisecond, did a dozen DOMEvent markers fire in a 20 ms window?

- Command: `profiler-cli thread markers --search "name:DOMEvent" --list --limit 0 --session S` (and `profile markers --search name:DOMEvent`), zoomed to a 570 ms range at t=85 s.
- Expected: a start time per row precise enough to order events a fraction of a millisecond apart.
- Got: every row printed `t=1m25s`: once the profile is past 60 s, times are rounded to the second, even inside a zoom narrower than a second. Same for `marker info` (`Time: 1m25s`).
- Workaround: `--list --json` and a script printing `flatMarkers[].start`.
- What the default output could show: sub-second times (e.g. `1m25.0736s`), at least when the zoom is under a few seconds.

## Question: what does the test log of a retry (`-2`) profile say?

- Command: `profiler-cli load <.../profile_browser_touch_all_events-2.js.json> --session S`, then `thread markers --category Test --search browser_touch_all_events.js --list --limit 0`.
- Expected: the parent process main thread selected by default, as with the first-run profile of the same job.
- Got: `t-24 (GeckoMain, WebExtensions)` was selected, and the command printed "0 markers ... No markers match", which reads like "no test log".
- Workaround: `profile info --search "Parent Process"`, then `thread select t-0`.
