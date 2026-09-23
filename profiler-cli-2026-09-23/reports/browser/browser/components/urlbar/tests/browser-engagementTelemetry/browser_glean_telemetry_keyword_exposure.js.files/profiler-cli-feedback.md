## Review: `profile-link.py` refuses a session loaded from a profiler link, which is how the review brief says to check links

- Command: `profiler-cli load "https://profiler.firefox.com/from-url/<encoded TC URL>/marker-table/?marker=52748&thread=0&v=17" --session S`, then `python3 profile-link.py --session S --marker m-175`
- Expected: a link to the new marker. The session's profile is the Taskcluster artifact, and the from-url link contains it.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link." It printed an empty link.
- Workaround: stopped the session and reloaded the raw Taskcluster URL. That cost one more 0.6 GB profile load per profile I needed a new link from.

## Review: loading a raw Taskcluster URL selects a content thread, not the parent main thread

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/NLRccogzSY-OXDtchUmreg/runs/0/artifacts/public/test_info/profile_browser_glean_telemetry_keyword_exposure.js.json --session S`
- Expected: t-0 (GeckoMain, Parent Process) selected, as when the same profile loads from its `thread=0` link.
- Got: `t-18 (GeckoMain, Privileged Content)` selected. My first `thread markers --search` returned nothing, and nothing said it had searched another thread.
- Workaround: `thread select t-0`.
