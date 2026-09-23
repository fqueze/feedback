## Question: at what time did each sample in a short window fall?

- Command: `profiler-cli thread samples-top-down --session <s>` after `zoom push 4.826,4.9` (74 ms, only 4 active samples)
- Expected: a way to list the samples in a range with their timestamps and stacks (e.g. `thread samples --list`), to place "remote-settings get ran here" between two markers.
- Got: aggregated trees only; the timestamps are lost.
- Workaround: repeated `zoom push` over 10-20 ms sub-windows and a grep per window, 4 round trips per profile.

## Question: which thread does a fresh load select? (review-browser_viewmodel.js)

- Command: `profiler-cli load 'https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/drt6j5shSpmjSVvQixa7pg/runs/0/artifacts/public/test_info/profile_browser_viewmodel.js.json' --session review-browser_viewmodel.js-4`
- Expected: the parent process GeckoMain (t-0) selected, as when loading the profiler.firefox.com link of the same profile with `thread=0`.
- Got: `Selected thread: t-17 (GeckoMain, WebExtensions)`; the next `thread markers --search storageOperationTime` silently returned 0 markers.
- Workaround: `thread select t-0` after every raw-URL load.
