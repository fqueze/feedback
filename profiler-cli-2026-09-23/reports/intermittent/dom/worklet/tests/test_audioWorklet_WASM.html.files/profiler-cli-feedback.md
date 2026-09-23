# profiler-cli feedback (review of test_audioWorklet_WASM.html)

## `zoom push` silently misreads the time format the tool itself prints

- Command: `profiler-cli zoom push 17m50s,18m20s --session review-test_audioWorklet_WASM.html-1`
- Expected: a zoom to 17m50s-18m20s (the `t=17m55s` format `thread markers --list` prints), or an error.
- Got: a zoom to 17s-18s, no warning; the following `thread markers` found 0 markers and I had to guess why.
- Workaround: `zoom push 1060,1095` (plain seconds).
- Suggestion: accept `XmYs` (and `XhYmZs`), or reject any unit suffix it cannot parse.
