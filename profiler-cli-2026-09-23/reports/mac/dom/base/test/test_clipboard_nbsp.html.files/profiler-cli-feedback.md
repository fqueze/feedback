## Log markers print "(empty)" for a message that is there

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <s>` and `profiler-cli marker info m-624 --session <s>` on the GeckoMain thread of the mochi.test content process, per-test profile `https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/Dr9CwGlaSViMZbjcCMwZ7w/runs/0/artifacts/public/test_info/profile_test_clipboard_nbsp.html.json`.
- Expected: `INFO — must wait for focus`, the SimpleTest `info()` line saying what `waitForFocus` was waiting for.
- Got: every `INFO` row reads `[(empty)] INFO: (empty)`, and `marker info` prints `Level: (empty)`, `Message: (empty)`. `marker info m-624 --json` has `"value": "must wait for focus"` with `"formattedValue": "(empty)"`, and the same for `level` (`"value": "INFO"`).
- Workaround: `marker info --json` one marker at a time. The test's own log was unreadable in the list view, which is the view the brief starts from; I only found the message because the waits were in doubt.

## Default session dir not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=test_clipboard_nbsp.html profiler-cli load '<taskcluster URL>' --session test_clipboard_nbsp.html-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`, as the error suggests. One round trip.

## Question: "which tests ran in this job, with their status and times?"

- Command: `profiler-cli thread markers --search "name:test" --list --limit 0` on the per-test profile's content main thread.
- Expected: the `test` markers only (one per test, with PASS/FAIL and path).
- Got: every `TEST-PASS` too, because `name:` also matches the `name` payload key of those markers (the guide warns about this for Text markers). Filtering on the marker name alone is not possible.
- Workaround: `--json` and a Python filter on `m['name'] == 'test'`.

## Question: "how busy was the machine while this test timed out?"

- Command: `profiler-cli thread markers --list --limit 0 --json --session <s>` on `https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/Dr9CwGlaSViMZbjcCMwZ7w/runs/0/artifacts/public/test_info/profile_resource-usage.json`, then a Python average of `data.cpuPercent` over the test's 145–472 s window. (Unfiltered, which the brief warns against: it took over 2 minutes and wrote a 142 MB file.)
- Expected: one command giving the CPU percentage (mean and peak) over a zoom range or over a marker's duration, e.g. `counter info` style stats for the `CPU Use` markers after `zoom push m-27`.
- Got: `CPU Use` rows show only name, time and duration. The percentage needs `marker info`, one marker at a time.
- Workaround: the JSON dump and a script.
