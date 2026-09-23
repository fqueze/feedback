## Log markers (mochitest `info()`) print "(empty)" for level and message

- Command: `profiler-cli thread markers --thread t-167 --category Test --list --limit 0 --session <s>` and `profiler-cli marker info m-38099 --session <s>` on
  https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/B3ErZi1EQ-6hiZie7ARnQw/runs/0/artifacts/public/test_info/profile_test_mouse_events_after_touchend.html.json
- Expected: `INFO  test_multi_touch: testing...` — the test's `info()` lines, which here are the only record of which events the test received and in what order.
- Got: `[(empty)] INFO: (empty)` in the list, and `Level: (empty)` / `Message: (empty)` in `marker info`. The `--json` output has `"value": "test_multi_touch: testing..."` with `"formattedValue": "(empty)"`: the formatter drops the value of `Log`-type markers' `level` and `message` fields.
- Workaround: `--list --json | jq '.flatMarkers[] | .fields | map(.value)'`.
- The question it should have answered: "what did the test log between its assertions".

## Question: "the machine's average and peak CPU over a time range" (from the review)

- Command: on https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/B3ErZi1EQ-6hiZie7ARnQw/runs/0/artifacts/public/test_info/profile_resource-usage.json, `profiler-cli zoom push m-1` (the `test` marker) then `profiler-cli thread markers --search "CPU Use"`, and the same with `--list --limit 0`.
- Expected: the mean and max of the `CPU Use` markers' `cpuPercent` field over the zoomed range, in the aggregate view.
- Got: only interval-duration stats (min 88 ms, avg 100 ms, max 125 ms), which say nothing about load; the values themselves only row by row (144 rows) in `--list`.
- Workaround: `--list --limit 0 --json`, then a Python mean/max over `flatMarkers[].data.cpuPercent`.
- Could have shown: min/avg/max of each numeric payload field per marker name in the aggregate view. For `CPU Use`, that answers "was the machine busy while this test ran" in one command.
