## Ordering two markers that print the same time

- Question: did the content-prefs transaction start before or after the TEST-UNEXPECTED-FAIL? Both print as `t=3.362s` / `t=3.363s` in `thread markers --list`, and the list shows durations but not end times, so "did transaction (5) end before location change X" needs arithmetic on rounded values.
- Command: `profiler-cli thread markers --search "content-prefs,TEST-" --list --limit 0 --session S`
- Expected: enough precision (0.1 ms) to order markers within one ms, or an end column for interval markers.
- Got: ms-rounded start only.
- Workaround: `profiler-cli marker info m-22 m-45 ... --json` piped to python for `start`/`end`.

## `marker info --json` with several handles: records lack their handle

- Command: `profiler-cli marker info m-22 m-354 m-17 --json --session S`
- Expected: each record carries `handle`, so the output can be matched to the handles asked for.
- Got: no `handle` key in the records (printed None); relied on the order of the arguments.

## `load` of a bare Taskcluster URL selects a WebExtensions thread (review)

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/.../profile_browser_sitespecific_image_zoom.js.json --session S`
- Expected: the parent process main thread (t-0) selected, as `profile info` lists it first and it holds the test's markers.
- Got: `Selected thread: t-14 (GeckoMain, WebExtensions)`. A query before `thread select t-0` would silently run on the wrong process.
- Workaround: `thread select t-0` right after every load.
