## Question: which markers belong to one page (innerWindowID)?

- Command: `profiler-cli thread markers --session <s> --search "innerWindowID:4294967338" --group-by name` (on the WebExtensions main thread).
- Expected: the markers whose `innerWindowID` is 4294967338; `--group-by field:innerWindowID` on the same thread does group by that value, so the field is known.
- Got: "0 markers (filtered from 340919)". `innerWindowID` is in `rawFields`, and `field:value` search seems to cover only the displayed fields.
- Workaround: `--search "waiting for paint" --list --limit 0 --json` and a Python filter on `data.innerWindowID`, to get the first and last tick of the leaked popup and the largest gap between ticks.
- What would answer it: `field:value` search matching `innerWindowID` too, and a "first/last/max gap" line in `--group-by` output.

## Question (review): during each of these 51 interval markers on thread A, how many markers of thread B fell inside, and what was the longest gap between them?

- Command: `profiler-cli thread markers --session <s> --thread t-0 --search "waiting for vsync" --list --limit 0 --json` and `thread markers --thread t-25 --search "waiting for paint" --list --limit 0 --json`, then a Python join on `start`/`duration` and `data.innerWindowID`.
- What would answer it: a way to count another search's markers per interval marker, e.g. `thread markers --search X --within "<thread>:<search>"`, printing count and max gap per interval. It is how a reviewer shows that "vsync stayed on" and "the leaked refresh driver ticked" happen together in every wait, and never in the waits that passed.
