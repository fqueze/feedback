## A raw Taskcluster profile opens on a WebExtensions thread, not the parent main thread

- Command: `profiler-cli load 'https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/Rg9VToKKTAqpJt6vYGxKQQ/runs/0/artifacts/public/test_info/profile_browser_trust_panel.js.json' --session …`, then `thread markers --search trustpanel …`.
- Expected: the parent process GeckoMain (t-0) selected, like the `thread=0` profiler link of the same profile.
- Got: `t-19 (GeckoMain, WebExtensions)` selected, so the search found "0 markers (filtered from 1)". Only the header said which thread was being searched.
- Workaround: `thread select t-0`. For a mochitest profile, the parent main thread would be a better default.
