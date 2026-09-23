# profiler-cli feedback (browser_rules_color_scheme_emulation_rdm.js)

## Question: in what order, to the millisecond, did these markers happen?

- Command: `profiler-cli thread markers --category Test --search browser_rules_color_scheme_emulation_rdm.js --list --limit 0 --session <s>` (and the same with `--search DOMEvent`, `--search PostMessage`)
- Expected: a start time precise enough to order events a few ms apart, e.g. `t=86.2860s`.
- Got: every row showed `t=1m26s`. The race I was diagnosing played out within 30 ms, all inside that one second. `marker info` also prints `Time: 1m26s - 1m26s (53.855ms)` with no sub-second start.
- Workaround: `--json` plus a python one-liner printing `start/1000` with 4 decimals, for every list I needed.
- What would have answered it: sub-second precision in the `t=` column (at least ms) once the view is zoomed to less than a few seconds, or always for `--list`.

## Same question, from the review (review-browser_rules_color_scheme_emulation_rdm.js)

- Command: `profiler-cli zoom push 86.25,86.38` then `profiler-cli thread markers --search "DOMEvent,PostMessageEvent,NotifyOffThread,responsive,Test" --list --limit 0 --session review-rdm-1`
- Expected: ms start times inside a 130 ms zoom.
- Got: every row `t=1m26s`, even though the view header says `(130ms)`.
- Workaround: the same `--json` plus python one-liner, a second time for the second profile.
