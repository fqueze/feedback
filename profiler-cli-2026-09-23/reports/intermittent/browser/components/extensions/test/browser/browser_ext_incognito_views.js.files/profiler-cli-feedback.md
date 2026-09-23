## When exactly did markers after the first minute happen?

- Command: `profiler-cli thread markers --search ... --list --limit 0` and `profiler-cli marker info m-8` on a 1m45s profile (Vcik-c_HQay1KotP_WCjqA, profile_browser_ext_incognito_views.js.json)
- Expected: millisecond timestamps (e.g. t=100.024s), as for markers before 60 s (t=29.444s).
- Got: every marker after 60 s printed as `t=1m40s`, also in `marker info` ("Time: 1m40s (instant)"), so the order and gaps of events within one second (popupshowing vs WM_ACTIVATE vs popuppositioned) could not be read.
- Workaround: `--list --json` piped through python to print `start/1000` with 3 decimals.
## Which thread does a query run on right after loading a raw Taskcluster profile URL? (review-browser_ext_incognito_views.js)

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/XmjpVDanQMe6224DZ2R2Hg/runs/0/artifacts/public/test_info/profile_browser_ext_incognito_views.js.json --session S`, then `profiler-cli thread markers --session S --search GrabFocus --list`
- Expected: the parent process GeckoMain (t-0), which the same profile opens on when it is loaded through its profiler.firefox.com link (`thread=0`).
- Got: the WebExtensions GeckoMain (t-37 here; t-35 and t-18 in two other profiles) was selected. The results were that process's `ReceiveMessage` markers, and they looked like parent-side markers until I checked the header line.
- Workaround: `thread select t-0` right after every `load`.
- Could show: make the parent main thread the default, or print the selected thread in the `load` output as the profiler link does.
- Correction to the entry above: the `load` status block most likely already names the selected thread. I cut it off with `tail -1`. The request that stands is to default to the parent main thread.
