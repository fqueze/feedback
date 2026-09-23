## Test-category `INFO` (Log) markers print "(empty)" for their message

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <s>` on thread t-15 (content process) of
  https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/WxvM9tABSlC0MzMMzzOYvw/runs/0/artifacts/public/test_info/profile_test_getUserMedia_basicScreenshare.html.json
  (also `marker info m-34`).
- Expected: `INFO  Testing screenshare without constraints`.
- Got: `[(empty)] INFO: (empty)`; `marker info` shows `Level: (empty)`, `Message: (empty)`.
- `marker info --json` shows `"value": "Testing screenshare without constraints"` with `"formattedValue": "(empty)"`,
  so the value is in the profile and only the formatting drops it (markerType `Log`, fields `level`/`message`).
- Workaround: `marker info <m> --json` per marker. This makes the test's own log unreadable in the list view,
  which is the first step of the diagnosis brief.
