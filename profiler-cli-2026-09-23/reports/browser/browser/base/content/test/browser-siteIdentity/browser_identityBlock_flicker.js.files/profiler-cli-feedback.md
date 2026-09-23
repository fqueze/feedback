## `profile info` does not say the buffer wrapped

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/b7ySXcb0QS6tqO1oN_urhA/runs/0/artifacts/public/test_info/profile_browser_smartwindow_prompts.js.json --session ...`, then `profile info`, then `thread markers --search ...`.
- Expected: `profile info` to say that the parent main thread's markers only start at 7m58s of a 9m47s profile.
- Got: `Full: 9m47s` and nothing else. The only sign was that a search for markers from the session's first minute returned "No markers match". I found out only by listing the first markers with `--limit 5`.
- Workaround: `thread markers --list --limit 5` to see where the data starts.
