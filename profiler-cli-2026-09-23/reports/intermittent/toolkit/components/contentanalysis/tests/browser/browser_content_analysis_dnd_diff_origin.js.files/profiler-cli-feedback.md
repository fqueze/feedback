## Default thread after `load` is not the parent main thread

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/X3LLfIE8SNez9k6jjqLQ3A/runs/0/artifacts/public/test_info/profile_browser_content_analysis_dnd_diff_origin-2.js.json" --session browser_content_analysis_dnd_diff_origin.js-2`, then `thread markers --category Test --search ... --list`
- Expected: the selected thread to be `t-0 (GeckoMain, Parent Process)`, as it was for the first profile of the same job.
- Got: `t-21 (GeckoMain, Privileged Content)` selected, so the Test-category search returned "No markers match the specified filters." with no hint that another thread holds them.
- Workaround: `profile info --search 'Parent Process'`, then `thread select t-0`. An empty marker search could say how many matches other threads have (as `profile markers --search` would).
