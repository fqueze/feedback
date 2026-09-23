# profiler-cli feedback (review-browser_aboutwelcome_fxa_signin_flow.js)

## Loading the raw Taskcluster URL selects a content thread, not the parent main thread

- Command: `profiler-cli load 'https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/IFpYdjuPTGuoAS5CF0Pd0g/runs/0/artifacts/public/test_info/profile_browser_aboutwelcome_fxa_signin_flow.js.json' --session <s>`, then `thread markers --search ... --list`
- Expected: t-0 (GeckoMain, Parent Process) selected, as when the same profile is loaded from a profiler link with `thread=0`.
- Got: t-18 (GeckoMain, Privileged Content) selected; the first query answered from the wrong process, noticed only from the header line.
- Workaround: `thread select t-0` after every load. For mochitest profiles, the parent main thread (where the test log lives) is the useful default.
