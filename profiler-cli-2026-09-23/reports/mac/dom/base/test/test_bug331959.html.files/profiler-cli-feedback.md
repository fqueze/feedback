## Log markers (mochitest INFO) print "(empty)" for their message

- Command: `profiler-cli marker info m-307 --session test_bug331959.html-1` (profile `profile_test_bug331959.html.json` of task RvTP9NRFTcyoxaOUB4101w), and every `thread markers --category Test --list` row for an `INFO` marker.
- Expected: `Message: must wait for load` (the SimpleTest.waitForFocus log line), in both the list row and `marker info`.
- Got: `[(empty)] INFO: (empty)` in the list, `Level: (empty)` / `Message: (empty)` in `marker info`. `--json` shows `"value": "must wait for load"` with `"formattedValue": "(empty)"`, so the formatter drops the value of `Log` markers' fields.
- Workaround: `marker info <handles> --json | jq '.fields'` for each INFO marker. This hides the test's whole INFO log (waitForFocus progress) from the default output, which is exactly what a focus-timeout diagnosis needs.

## The resource-usage profile of a job killed at maxRunTime does not load

- Command: `profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/FW58XVpxQTS1nW4RZ6d1fg/runs/0/artifacts/public/test_info/profile_resource-usage.json" --session test_bug331959.html-6`
- Expected: the job timeline (the `test` markers, CPU use), as for a job that finished.
- Got: `Error: Profile load failed: Unserializing the profile failed: SyntaxError: Unexpected non-whitespace character after JSON at position 11524 (line 2 column 1)`. The artifact is the harness's streaming form: one JSON object per line (`{"type":"meta",...}` then `{"type":"marker",...}`), left unfinished when the task was killed. Every job killed at maxRunTime has one, and those are the jobs where the timeline matters most.
- Workaround: `curl` it and `jq 'select(.type=="marker" and .name=="test")'`, which gives no links.

