## Log markers (mochitest INFO) show "(empty)" for Level and Message

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session test_device_sensor_event.html-1` (thread t-17, the mochi.test content process), then `profiler-cli marker info m-230`.
- Expected: the INFO text, e.g. `INFO: must wait for focus`.
- Got: every INFO row reads `[(empty)] INFO: (empty)`, and `marker info` shows `Level: (empty)` / `Message: (empty)`. `marker info m-230 --json` has `"value": "must wait for focus"` with `"formattedValue": "(empty)"` — the formatter drops string values of `Log` markers.
- Workaround: `marker info --json` per marker, reading `fields[].value`. The question it could not answer: "what did the test log (INFO) before it timed out".
- Profile: https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/BMYU5cgaSUyNKx7-zX99Zg/runs/0/artifacts/public/test_info/profile_test_device_sensor_event.html.json

