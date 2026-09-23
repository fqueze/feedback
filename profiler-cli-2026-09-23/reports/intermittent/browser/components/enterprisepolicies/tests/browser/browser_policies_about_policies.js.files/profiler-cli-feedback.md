# profiler-cli / profile-link.py feedback (review of browser_policies_about_policies.js)

## profile-link.py refuses a session loaded from a report's profiler.firefox.com link

- Command: `profiler-cli load '<profiler.firefox.com/from-url/...marker=67040&thread=0&v=17>' --session S`, then `python3 profile-link.py --session S --marker m-12`
- Expected: a link. The review brief says to open each report link with `profiler-cli load <link>`, and that URL wraps the raw Taskcluster URL, so the script could unwrap it.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link."
- Workaround: stop the session and load the Taskcluster URL again, which costs another 0.6 GB download and parse.

## Loading a raw Taskcluster URL selects a WebExtensions thread, not the parent main thread

- Command: `profiler-cli load 'https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/PHKrWUFGRX-rTYeOeuxi6A/runs/0/artifacts/public/test_info/profile_browser_policies_about_policies-2.js.json' --session S`
- Expected: t-0 (GeckoMain, Parent Process), which has the most CPU (5744 ms) and markers (76,796).
- Got: `Selected thread: t-13 (GeckoMain, WebExtensions)`. A `thread markers --search` then printed "No markers match", with nothing saying that the wrong thread was selected.
- Workaround: `thread select t-0`.

## A load that fails with 503 still leaves a session registered

- Command: `profiler-cli load '<resource-usage profiler link>' --session S`
- Got: "Profile load failed: ... 503 Service Unavailable". `session list` still showed S with a daemon pid, so the retry needed `stop S` first.
- Expected: no session left behind after a failed load, or a load that retries on a 503.
