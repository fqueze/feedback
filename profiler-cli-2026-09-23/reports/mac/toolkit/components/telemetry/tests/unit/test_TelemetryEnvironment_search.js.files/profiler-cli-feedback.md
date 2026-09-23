## Question: "how busy was the machine while this one test ran?" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` after `zoom push <test marker>`
- Expected: each CPU Use marker's CPU %, idle %, or a summary (time-weighted mean busy %, min idle %) over the zoomed range; `counter list` could also have shown it as a track.
- Got: `counter list` says "No counters in this profile"; the `--list` rows show only name, start and duration, no field values. The CPU figures are only in `marker info` one at a time, or `--json`.
- Workaround: `--json` and a Python script over `flatMarkers[].data.idle_pct`, weighted by duration, per minute and over the test's range.

## The same search returned nothing, then matched a few commands later

- Session: `test_TelemetryEnvironment_search.js-1` (profile_test_TelemetryEnvironment_search.js.json of task N2IVMz2JRS28eBvWkwlp-Q), status "View range: Full profile", no filters.
- Commands: `profiler-cli thread samples-bottom-up --include-idle --search nss3 --limit 5` printed "No samples matched --search \"nss3\"", and `profiler-cli thread functions --include-idle --search yy_` printed "0 functions (filtered from 273)".
- A few commands later, after a `zoom push 17.06,26.62` / `zoom pop` pair, the very same two commands (status again "Full profile", no filters) matched `nss3.dll!yy_find_reduce_action` and `nss3.dll!yy_reduce` under `GleanBoolean.testGetValue`. The function count also differed (273 vs over 890 handles in use).
- Expected: the same result for the same command and the same session state.
- Workaround: re-run the query inside a zoom; I did not find what made the first calls come back empty.


## A loaded URL's `search=` silently restricts every sample query (review-test_TelemetryEnvironment_search.js)

- Command: `profiler-cli load '<…profile_test_TelemetryEnvironment.js.json/calltree/?range=0m121000&thread=0&search=testGetValue&v=17>' --session <s>`, then `thread samples --include-idle`.
- Expected: either the whole thread, or the URL's search shown as an active filter in the load output and in `status`, so `filter clear` removes it.
- Got: "4745 running samples", and every function at 100% of them. `status` said "Filters: none", and `filter clear` and `zoom clear` changed nothing. Only `thread functions --search X`, with its "% of view, % of full" pair, showed that the view was cut to 93.4% of the thread.
- Workaround: `stop`, then reload the same URL without `search=`. That gave 5079 samples.

## Question: "where does the time go between consecutive log lines of the test?"

- Command: `thread markers --category Test --list --limit 0` on a timed-out test, then a script over its text rows to get the gaps between consecutive `TEST-PASS` markers, sorted, summed, and with the message that ends each gap.
- What would have answered it: a `--gaps` option on `--list` (or `thread markers --search name:TEST-PASS --gaps`), printing each gap above a threshold with the markers on either side, plus the count, sum and mean.
