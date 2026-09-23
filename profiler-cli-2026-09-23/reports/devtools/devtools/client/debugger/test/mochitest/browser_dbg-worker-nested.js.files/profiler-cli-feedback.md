## Question: in what order did these events on two threads happen, within the same millisecond?

- Command: `profiler-cli thread markers --thread t-201 --list --limit 0` (and the same on t-200, t-202)
- Expected: start times precise enough to order markers across threads (sub-ms), since `--list` is the chronological view.
- Got: `t=3m52s` for every marker of interest; the whole race (worker thread wake at 232411.74 ms vs main thread dispatch at 232411.9 ms) is invisible at that resolution.
- Workaround: `--json | jq '.flatMarkers[] | .start'`. The text list could print ms precision (or relative to the zoom start) when the range is narrow, or have a `--precise-times` flag.

## Default selected thread is not the parent main thread

- Command: `profiler-cli load <profile_browser_dbg-worker-nested-2.js.json> --session X` then `profiler-cli thread markers --session X --category Test --search browser_dbg-worker-nested.js --list --limit 0`
- Expected: the test log (parent GeckoMain), as on the first profile, where t-0 was auto-selected.
- Got: `0 markers` on `t-15 (GeckoMain, WebExtensions)`, which had been auto-selected; nothing hinted that the Test markers live on another thread.
- Workaround: `--thread t-0`. Selecting the parent process main thread by default, or saying "N Test markers exist on t-0" when the filter matches nothing, would avoid it.
