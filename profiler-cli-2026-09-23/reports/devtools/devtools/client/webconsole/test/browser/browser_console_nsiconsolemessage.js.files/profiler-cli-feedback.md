
## `zoom push` silently accepts `23m5s,23m32s` as a zero-length range at 23 s (review-browser_console_nsiconsolemessage.js)

- Question: what the harness logged between the last test of the manifest and the LeakSanitizer report (t=23m5s to t=23m32s, as printed by `thread markers`).
- Command: `profiler-cli zoom push 23m5s,23m32s --session review-browser_console_nsiconsolemessage.js-1`
- Expected: a 27 s range from 23m5s to 23m32s (the same time format `thread markers` prints), or an error.
- Got: `Pushed view range: ts-1 (23s) to ts-1 (23s) (duration: 0s)`, then `No markers match` from the next query, with no warning.
- Workaround: converted to seconds by hand, `zoom push 1385,1412`.
