## `zoom push` silently misreads the time format the tool itself prints

- Command: `profiler-cli zoom push 16m0s,17m5s --session test_http3_fast_fallback.js-7`
- Expected: a zoom to 960 s – 1025 s. Marker lists print times as `t=16m14s`, so that is the obvious format to paste back. Failing that, an error.
- Got: exit 0 and a 1 s view (`View: ts-1→ts-2 (1s)`) at the start of the profile. The next `thread markers` call listed the job's first two markers (`selftests`, `testAssertStack`), which looked like "nothing ran in that window".
- Workaround: seconds, `zoom push 900,1025`.
