## Default selected thread is a WebExtensions GeckoMain, not the parent's

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/LjvxsR5DRUCMLiNDzT38iw/runs/0/artifacts/public/test_info/profile_browser_styleeditor_filesave-2.js.json --session browser_styleeditor_filesave.js-2`, then `thread markers --category Test --search TEST-UNEXPECTED --list`
- Expected: the parent process GeckoMain (t-0) selected, as it was for the first-run profile of the same test in the same job.
- Got: `t-17 (GeckoMain, WebExtensions)` selected; "No markers match the specified filters", which reads like "no failure" rather than "wrong thread".
- Workaround: `thread select t-0` after `profile info --search "Parent Process"`. An empty result on a non-parent thread for `--category Test` could hint that Test markers live on the parent main thread.
