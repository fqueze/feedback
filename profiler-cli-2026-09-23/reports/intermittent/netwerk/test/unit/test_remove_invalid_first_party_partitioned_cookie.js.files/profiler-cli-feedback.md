## `zoom push` past the end of the profile does not warn

- Command: `profiler-cli zoom push 244,312 --session S` on a 312 ms profile (I meant ms)
- Expected: a warning that 244 s to 312 s lies beyond the profile's 312 ms.
- Got: `View: ts>10l→ts>10y (1m8s)`, and every later query returned 0 markers with no explanation.
- Workaround: `zoom push 0.244,0.312`. Accepting an `ms` suffix, or warning when the range falls outside the profile, would save the retry.
