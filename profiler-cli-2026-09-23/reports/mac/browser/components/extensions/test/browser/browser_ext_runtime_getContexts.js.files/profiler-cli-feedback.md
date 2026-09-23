## Question: where does a steady series of instant markers stop, or pause (review-browser_ext_runtime_getContexts.js)

- Command: `profiler-cli thread markers --search "RefreshDriverTick waiting for paint"` on the WebExtensions GeckoMain thread (12,000 instant markers over 216 s), to check a claim that they run "from t=6.893 s with no gap over 1 s until the end".
- Expected: the frequency analysis to say where the series has its gaps, since it already computes them.
- Got: `interval: min=5.375μs, avg=17.782ms, max=3.682s` — the size of the longest gap, but not where it is, nor how many gaps exceed some size. The 3.682 s gap turned out to be before the claimed start, which the output could not tell.
- Workaround: `--list --limit 0 --json` to a file, then a Python script over `flatMarkers[].start` listing gaps over 0.5 s and 1 s.
- What would have answered it: the start time (or the handles) of the longest interval in the frequency analysis, or a `--min-gap <ms>` option listing the gaps of a marker series.
