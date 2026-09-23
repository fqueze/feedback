## `zoom push` silently accepts a range past the profile's end, with an unknown unit suffix (review-test_breakpoint-20.js)

- Command: `profiler-cli zoom push 388.2m,400m --session review-test_breakpoint-20.js-1` (the `m` suffix copied from the profiler URL's own `range=400m29879` syntax, where it means ms).
- Expected: either ms, as in the URL, or an error for an unknown suffix / a range beyond the 30.28 s profile.
- Got: `ts>4J` to `ts>4l`, i.e. 388.2 s to 400 s (6m28s–6m40s), far past the profile end, then "0 markers in view" with no warning.
- Workaround: `zoom push 388.2ms,400ms`.
