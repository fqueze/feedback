## Empty call tree with no "no samples" note

- Command: `profiler-cli zoom push m-171 --session test_h2_unclean_shutdown_retry.js-3` (a 50 ms `Jank` marker), then `profiler-cli thread samples-top-down --limit 60 --session test_h2_unclean_shutdown_retry.js-3`
- Expected: a line saying the range holds 0 samples (or N idle samples excluded), so I know the thread was not sampled rather than the output being cut off.
- Got: the header, "Top-Down Call Tree:", and nothing else.
- Workaround: re-ran with `--include-idle` over a wider range to see how many samples existed at all (6 in 250 ms on this macOS profile).
