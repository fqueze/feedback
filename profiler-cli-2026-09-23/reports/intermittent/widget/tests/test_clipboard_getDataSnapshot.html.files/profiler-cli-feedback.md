## Log markers (mochitest `info()` lines) print as "(empty)"

- Command: `profiler-cli thread markers --session test_clipboard_getDataSnapshot.html-2 --category Test --list --limit 0` on the content main thread of https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/LU5S0VZRQwatSCyaqW1zxw/runs/0/artifacts/public/test_info/profile_test_clipboard_getDataSnapshot.html.json, and `profiler-cli marker info m-3031`.
- Expected: the test's `info()` text, e.g. `INFO check clipboard data again`.
- Got: every `INFO` row reads `[(empty)] INFO: (empty)`, and `marker info` shows `Level: (empty)`, `Message: (empty)`. `--json` has `"value": "check clipboard data again"` with `"formattedValue": "(empty)"`, so the formatter drops a value that is there (marker type `Log`, fields `level` and `message`).
- Cost: the test log lost its section headers (`Test getDataSnapshot request after write on 1`), so I had to take which add_task failed from the FAIL marker's stack instead.
- Workaround: `marker info <handle> --json` and read `fields[].value`.

## review-test_clipboard_getDataSnapshot.html: how many samples fall inside one marker

- Question: how many samples does the parent main thread have during the 49.7 ms `PContent::Msg_SetClipboard` runnable, and how many of them are idle?
- Command: `profiler-cli zoom push m-3013 --session review-test_clipboard_getDataSnapshot.html-2`, then `profiler-cli thread samples-top-down --include-idle` and the same without `--include-idle`, on the parent main thread (t-0) of https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/LU5S0VZRQwatSCyaqW1zxw/runs/0/artifacts/public/test_info/profile_test_clipboard_getDataSnapshot.html.json.
- Expected: a sample count on each node, or at least the total in the header, since 100% of 2 samples and 100% of 200 samples mean different things.
- Got: percentages only. The count (`"totalSamples": 2`) is only in `--json`.
- Workaround: `--json` and read `regularCallTree.totalSamples`.
