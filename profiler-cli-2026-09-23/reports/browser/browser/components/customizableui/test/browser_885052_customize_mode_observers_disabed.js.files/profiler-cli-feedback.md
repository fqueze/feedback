## review-browser_885052: samples-top-down silent on a range with no samples

- Command: `profiler-cli zoom push 22.316,22.640 --session <s>; profiler-cli thread samples-top-down --include-idle --limit 15 --session <s>`
- Expected: a note that the view has no samples, as `thread samples` prints ("No samples in the current view.").
- Got: only the header and "Top-Down Call Tree:" with nothing under it, so it looked like a truncated or broken result.
- Workaround: ran `thread samples` to find out the range had 0 samples. It was a 324 ms `Jank` marker with no samples at all.
