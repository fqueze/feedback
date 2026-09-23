## Log (Test category INFO) markers print "(empty)" for their fields

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session test_pointerlock_xorigin_iframe.html-1` (thread t-18, content process of an xorig mochitest), then `profiler-cli marker info m-1111`.
- Expected: `INFO  Test finish` / `Level: INFO, Message: Test finish`.
- Got: `[(empty)] INFO: (empty)` in the list and `Level: (empty) / Message: (empty)` in marker info. `--json` shows `"value": "Test finish"` but `"formattedValue": "(empty)"`.
- Workaround: `marker info --json` per marker and read `.fields[].value`. This hides the test's own `info()` log, which is the main thing a test's log is read for.
- Profile: https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/JR9efRP4SZCklRW5U5dA-A/runs/0/artifacts/public/test_info/profile_test_pointerlock_xorigin_iframe.html.json

## Searching for a marker's displayed label finds nothing (review-test_pointerlock_xorigin_iframe.html)

- Command: `profiler-cli thread markers --thread t-0 --search "focus - ,activate - " --list --limit 0 --session review-test_pointerlock_xorigin_iframe.html-1`
- Expected: the `focus - browser@…` / `activate - window` DOMEvent rows that the list shows.
- Got: none. Only `eventType:focus,eventType:activate` matches. The `--list` label (`focus - browser@14156c040`) cannot be searched by the text it shows.
- Workaround: `eventType:<type>`. Suggestion: also match the formatted label, or name the field to use in the "no match" message.

## Question: "was this task posted from inside that runnable?" (review-test_pointerlock_xorigin_iframe.html)

- Command: `profiler-cli thread markers --thread t-11 --list` in a 30 ms zoom, then `marker info m-23 m-53 m-36 --json` to compare start times.
- The list prints `t=1.700s` for the `start` runnable, the `TaskController::AddTask (flow=…)` and the `AsyncEventDispatcher` runnable, so it cannot show that the AddTask falls inside the runnable's 227 µs. Only the `--json` `start` values do. Flow ids also get reused (the same flow is added twice, 1.4 ms apart, in the Windows profile).
- What could answer it: for a `Runnable` marker with a `task` id, list the `AddTask` for its flow that came *before* it, with the enclosing runnable at the moment of the AddTask ("posted during start[…]:JS"). Or let `--list` print sub-ms times when the zoom is under ~100 ms.
