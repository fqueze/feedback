## Log (INFO) markers print "(empty)" for their message in text output

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session test_peerConnection_glean.html-1` (thread t-22, content process of a plain mochitest, profile https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/SMD2RIliSKGlz4X1Ss3eOQ/runs/0/artifacts/public/test_info/profile_test_peerConnection_glean.html.json), also `marker info m-27591`.
- Expected: `INFO  Done running test: checkDtlsVersion1_0` — the test's own progress log.
- Got: `INFO  [(empty)] INFO: (empty)` for every INFO marker; `marker info` shows `Level: (empty)`, `Message: (empty)`. The `--json` output has `"value": "Done running test: checkDtlsVersion1_0"` with `"formattedValue": "(empty)"`, so the formatter drops a present string value.
- Workaround: `marker info <m> --json` per marker. For a test log of a few hundred lines this means a script over the JSON just to read the INFO lines.

