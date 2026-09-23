## Loading the resource-usage profile of a task killed at max-run-time

- Command: `PROFILER_CLI_SESSION_OWNER=browser-test_menu_button.html profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/d6XM1k2_Sjquqb1U6OjEpQ/runs/0/artifacts/public/test_info/profile_resource-usage.json" --session browser-test_menu_button.html-ru2`
- Expected: the job's timeline up to the kill, or at least an error saying the file is a partial, streamed profile.
- Got: `Error: Profile load failed: Unserializing the profile failed: SyntaxError: Unexpected non-whitespace character after JSON at position 11527 (line 2 column 1)`. The failed load also left the session registered in `profiler-cli session list` with a live daemon pid, and it had to be stopped by hand.
- Workaround: none for the job timeline. Used the job's per-test profiles and Treeherder instead.
