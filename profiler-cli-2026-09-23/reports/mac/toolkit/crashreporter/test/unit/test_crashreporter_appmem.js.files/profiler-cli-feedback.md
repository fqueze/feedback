
## review-test_crashreporter_appmem.js: how many samples are in the zoomed range?

- Command: `profiler-cli thread info --session <s>` after `zoom push 0.535,1.235`.
- Expected: the sample count for the zoomed range, since the header says `View: ts-i→ts-z (700.00ms)`.
- Got: "This thread contains 35 samples", which is the count for the whole thread. The only in-range count was `categoryBreakdown.totalSamples` (23) in `thread samples --include-idle --json`.
- Workaround: read `categoryBreakdown.totalSamples` from `thread samples --json`. The default text of `thread samples` could print "N samples in view" in its header.
