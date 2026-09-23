## Log markers (Test category INFO) print "(empty)" although their payload has text

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <s>` on thread GeckoMain of `http://mochi.test`, in the per-test profile https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/Dr9CwGlaSViMZbjcCMwZ7w/runs/0/artifacts/public/test_info/profile_test_content_iterator_pre_order.html.json ; also `marker info m-216`.
- Expected: the INFO line's message, e.g. `must wait for load` / `must wait for focus` (the test's own log, which the brief says to read first).
- Got: `[(empty)] INFO: (empty)` in the list, and `Level: (empty)` / `Message: (empty)` in `marker info`. `marker info --json` has `"value": "must wait for load"` with `"formattedValue": "(empty)"`, so the formatter drops the value.
- Workaround: `marker info <handles> --json | jq '.fields'` per marker.

## TestStatus timeout marker lists with an empty description

- Command: same `thread markers --category Test --list`.
- Expected: the `TEST-UNEXPECTED-FAIL` for a timeout to show `Test timed out.` in the list row.
- Got: an empty description; the text is only in the `Subtest` field, visible in `marker info`. Rows whose `Message` is empty could fall back to `Subtest`.
- Workaround: `marker info` on each unlabelled TEST-UNEXPECTED-FAIL.

## `--search` does not match the message of Log markers

- Command: `profiler-cli profile markers --search 'Unable to restore focus' --session <s>` on https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/doFEz5QUQFS8OKEgELLz9w/runs/0/artifacts/public/test_info/profile_test_content_iterator_pre_order.html.json
- Expected: the harness's `INFO` Log markers on the `http://mochi.test` GeckoMain whose `message` is `Error: Unable to restore focus, expect failures and timeouts.` (dozens of them).
- Got: `No markers match the specified filters (searched 16 threads).` The same search does match in the job's resource-usage profile, where the text is in a TestStatus marker. Probably the same formatter as above: the Log payload value formats to "(empty)", and search looks at the formatted value.
- Workaround: `thread markers --category Test --list --limit 0 --json` and filter `data.message` in a script. The question the default output could not answer: "what did the test harness log, in order, with times" — the brief's first step.
