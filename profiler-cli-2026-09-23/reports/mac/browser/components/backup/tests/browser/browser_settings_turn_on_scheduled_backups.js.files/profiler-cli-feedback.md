## Nothing says the thread's data starts 97 s into a 99 s profile

- Command: `profiler-cli profile info --session browser_settings_turn_on_scheduled_backups.js-1` and `thread info` on t-0, for https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/N8WOkP63RUa4uJ8MOuClcw/runs/0/artifacts/public/test_info/profile_browser_settings_turn_on_scheduled_backups.js.json
- Expected: a warning that the parent main thread's samples and markers only cover 1m37s-1m39s of the "Full: 1m39s" range, because 7M `DocAccessible::ContentRemovedNode` markers filled the 537 MB buffer and older data was evicted.
- Got: `Full: 1m39s` everywhere, `This thread contains 244 samples and 7036534 markers`, and a CPU activity line for 1m37s-1m39s. The test log search then returned only the last 17 Test markers, which at first looked like a search problem, not data loss.
- Workaround: compared the thread's sample count with the 10 ms interval, and read the test's log from the resource-usage profile instead.
- Suggestion: in `profile info` / `thread info`, print the time range of the thread's first and last retained sample and marker when it is much shorter than the profile, and name the buffer overflow when the buffer is full.

