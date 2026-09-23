## Marker times above 60 s lose sub-second precision in --list

- Command: `profiler-cli thread markers --session browser_dbg-bfcache.js-1 --category Test --search browser_dbg-bfcache.js --list --limit 0`
- Expected: times precise enough to line markers up (e.g. `t=87.233s`), as they are below 60 s (`t=30.850s`).
- Got: `t=1m27s` for every marker between 87.0 and 87.99 s, so "checking for open popups" and the vsync wait start, 0.4 s apart, print the same time; zoom ranges had to be guessed.
- Workaround: `--json` and a Python one-liner printing `start/1000`.

## Question: over which intervals did a repeating marker keep repeating?

- Question: when did the parent's `RefreshDriverTick waiting for paint` (a ~60/s instant marker) start, and did it stop between tests?
- Command: `profiler-cli thread markers --session browser_dbg-bfcache.js-1 --search 'RefreshDriverTick waiting for paint' --list --limit 0 --json` piped into a script clustering starts with gap > 200 ms.
- What the output could have shown: the default aggregate already prints rate stats (min/avg/max gap); a list of "bursts" (start, end, count) for instant markers, split at gaps much larger than the median, would answer it directly.

## Question: are there samples at all in this range?

- Command: `profiler-cli thread samples --session browser_dbg-bfcache.js-1` zoomed on 36.4-37.8 printed "No samples in the current view" with an empty heaviest-stack section.
- It would help to say whether the thread had no samples (profiler paused / recording gap) or only idle samples, since those mean different things when a periodic marker disappears.

## Question: when did a marker name last occur on a thread? (review-browser_dbg-bfcache.js)

- Question: after which time did the parent's ordinary `RefreshDriverTick` stop (while `RefreshDriverTick waiting for paint` went on)? And the same per content process.
- Command: `profiler-cli thread markers --session review-browser_dbg-bfcache.js-1 --search RefreshDriverTick` gives count and interval stats per name, but no first/last time. Workaround: `--list --limit 0 --json` and a script printing first/last `start` per name, on each of 8 threads.
- What the output could have shown: a first/last timestamp next to each name in the "By Name" aggregate, which answers "did X stop, and when" at once.
- Also: the header count of `--search Composite` on the Renderer (560) includes the 279 `TimingDistribution::accumulate` markers, which match on their payload. The report under review took 560 as the number of Composite markers.
