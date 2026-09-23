## `load` of a CI per-test profile selected the WebExtensions thread, not the parent main thread

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/Tl9NhCHESfi3zXEezJ-g1g/runs/0/artifacts/public/test_info/profile_browser_download_starts_in_tmp.js.json --session S`, then `profiler-cli thread markers --session S --category Test --search browser_download_spam_protection --list --limit 0`
- Expected: the test log, as with the Windows profile of the same test, where `load` selected `t-0 (GeckoMain, Parent Process)`.
- Got: "No markers match the specified filters." The selected thread was `t-18 (WebExtensions)`; only the header line said so.
- Workaround: `profile info --search GeckoMain`, then `thread select t-0`.
- What would have helped: select the parent process main thread by default (the Windows profile got it), or, when a `--category Test` search finds nothing on a non-parent thread, say that the Test markers are on `t-0`.

## Question: "which marker in this session is the `marker=N` of a report's link?" (review)

- Command: `profiler-cli marker info m-11 --session S`, then `profiler-cli marker info m-11 --session S --json | python3 -c '...print(d["markerIndex"])'`, once per quoted marker (12 markers over two profiles).
- Expected: the default `marker info` output to show the marker index a profiler.firefox.com URL uses, or `thread markers --list` to accept a marker index (e.g. `marker info idx:200472`) and resolve it to its handle.
- Got: the default output shows Type, Time, Fields and stack, but not the index; only `--json` has `markerIndex`.
- Workaround: a python one-liner over `--json` per handle.
