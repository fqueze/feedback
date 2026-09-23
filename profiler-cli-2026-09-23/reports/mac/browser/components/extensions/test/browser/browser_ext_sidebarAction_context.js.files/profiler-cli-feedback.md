# profiler-cli feedback (browser_ext_sidebarAction_context.js)

## A search term containing ":" silently becomes a field filter

- Command: `profiler-cli thread markers --search "browser-sidebar.js:2342" --list --limit 0 --json --session …`
- Expected: the `setTimeout callback` markers whose text contains `(chrome://browser/content/sidebar/browser-sidebar.js:2342)`.
- Got: an empty list, no warning. The term was read as field `browser-sidebar.js`, value `2342`, a field no marker has.
- Workaround: search for a substring without a colon (`loadingTimerID`).
- Suggestion: warn when a `field:` prefix names no payload key of any marker, or fall back to a substring match.

## Question: "when was this timer armed, and how long did it wait before running?"

- Needed a shell loop over `profiler-cli marker info <h>` for 17 handles, grepping `Time:` and `Captured at:`.
- `thread markers --list` shows only the run time. For `setTimeout callback` (TextStack) markers, the stack capture time is when the timer was armed, so a "captured at" or "armed … ago" column (or `--show-stack-time`) would have answered it in one command. It was the key observation of this report: one 0 ms timer armed at 4.193 s ran at 4.228 s.

## Question: "is the sampler keeping its interval in this range?"

- Needed `thread samples --include-idle --json` plus `categoryBreakdown.totalSamples` per zoom window, in a loop.
- Default `thread samples` output shows percentages, not the number of samples, except "0 running samples". A sample count and the effective mean interval next to the configured one (10 ms here, effectively 35-60 ms) in `thread info` or `thread samples` would have answered it.

## JSON time bases differ

- Command: `profiler-cli zoom push 4.100,4.150` then `thread samples --json`.
- `context.currentViewRange.start` is 4107.6 (absolute, profile zero 7.6 ms earlier), while marker `start` values in `thread markers --json` and `zoom push` arguments use the other base (4227.68 ms prints as t=4.228s and a 4.2275,4.2280 zoom includes it).
- Cost: had to test with a narrow zoom to find out which base the marker times use.

## `profile info` on a resource-usage profile says "No significant activity"

- Command: `profiler-cli profile info --session <resource-usage profile>`
- Got: "CPU activity over time: No significant activity." and `counter list`: "No counters in this profile."
- The machine's CPU use is there, as 12,440 `CPU Use` markers (100 ms each, with `CPU Percent`, `Idle %`). A pointer to them from `profile info` would save the search.

## Question (review): "was the main thread idle while this overdue timer waited?"

- Needed `thread markers --search name:Runnable --list --limit 0 --json` plus a script that merges the Runnable intervals and prints the gaps between them, for 4.180–4.2285 s.
- `thread markers --list` shows each task but not the idle time between them; a `thread idle-gaps` (or a gap column / `--show-gaps` on `--list`) would have said in one command whether a runnable could have been waiting in the queue.

## Question (review): "did the sampler stop for every thread, or only this one?"

- Needed the raw profile JSON (downloaded and gunzipped outside the tool), to line up each thread's sample times in 4.10–4.30 s.
- `thread samples` gives one thread's count per zoom. A per-process list of sampler ticks (or `profile info` showing effective sampling interval and longest gap per process) would have answered whether the gaps were in the sampler's sleep or in the cost of sampling.
