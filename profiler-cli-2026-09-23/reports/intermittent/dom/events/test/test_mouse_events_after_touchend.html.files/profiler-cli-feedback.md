## Question: what did the test log with info() (its own log), in a mochitest-plain-xorig profile?

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <s>` on the content thread of `http://mochi.xorigin-test` (profile https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/X_mo1nkiSGKkLwUcaYu3tw/runs/0/artifacts/public/test_info/profile_test_mouse_events_after_touchend-2.html.json), then `marker info m-36`.
- Expected: `INFO  test_single_tap_with_consuming_touchend: testing...` and the test's `Received: {...}` lines.
- Got: every `Log` marker prints as `[(empty)] INFO: (empty)`, and `marker info` shows `Level: (empty)`, `Message: (empty)`. `marker info --json` has `"value": "test_single_tap_with_consuming_touchend: testing..."` with `"formattedValue": "(empty)"`, so the value is there and only its formatting is lost (the `Log` marker schema's field format is probably one the CLI does not know).
- Workaround: `thread markers --search name:INFO --list --json` and reading `fields[].value` with a script.
- Could have shown: the value, like TestStatus markers do.
## Question (review-test_mouse_events_after_touchend.html): which `test` marker is this test's failing retry, in a resource-usage profile?

- Command: `profiler-cli thread markers --search 'FAIL — dom/events/test/test_mouse_events_after_touchend' --list --session <s>` on https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/X_mo1nkiSGKkLwUcaYu3tw/runs/0/artifacts/public/test_info/profile_resource-usage.json
- Expected: the `test` markers whose displayed label is `FAIL — dom/events/test/test_mouse_events_after_touchend.html`.
- Got: "No markers match". `--search FAIL` and `--search dom/events/...` each match separately. The label is composed from two fields, and the search does not match text that spans them.
- Workaround: search the path, then filter on `name == 'test'` with a script.
- Could have shown: search matching the label as displayed.

## Question (review-test_mouse_events_after_touchend.html): what was the machine's CPU use during one test, in a resource-usage profile?

- Command: `profiler-cli profile info` / `profiler-cli counter list` on the same profile.
- Expected: the machine CPU track.
- Got: `profile info` says "No significant activity", and `counter list` says "No counters in this profile". The data exists only as `CPU Use` markers (`cpuPercent` field), which needed a `--json` script to summarize over a range.
- Could have shown: a min/median/max of `CPU Use` over the zoom range, or a pointer to those markers from `profile info`.
