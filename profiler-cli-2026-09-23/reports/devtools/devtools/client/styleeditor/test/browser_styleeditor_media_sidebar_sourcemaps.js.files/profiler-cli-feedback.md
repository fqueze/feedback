## `load` picks a WebExtensions GeckoMain as the default thread

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/JQZgTPShQvS04PtDY5jiiA/runs/0/artifacts/public/test_info/profile_browser_styleeditor_media_sidebar_sourcemaps-2.js.json --session <s>`, then `thread markers --category Test ...`
- Expected: the Parent Process GeckoMain (t-0) selected, as with the first-run profile of the same job.
- Got: `t-16 (GeckoMain, WebExtensions)` was selected, so the Test marker query returned "No markers match". Only the `[Thread: ...]` header showed why.
- Workaround: `profile info --search GeckoMain`, then `thread select t-0`.
