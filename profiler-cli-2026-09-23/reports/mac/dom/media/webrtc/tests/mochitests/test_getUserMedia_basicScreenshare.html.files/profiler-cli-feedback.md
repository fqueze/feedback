# profiler-cli feedback

## Log markers of the Test category print "(empty)" for their message

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session gum-screenshare-2` on the content process thread of
  https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/WxvM9tABSlC0MzMMzzOYvw/runs/0/artifacts/public/test_info/profile_test_getUserMedia_basicScreenshare-2.html.json
- Expected: the test's `info()` lines, e.g. `INFO  Call getUserMedia for {"video":{"mediaSource":"screen"}}`.
- Got: every INFO row reads `[(empty)] INFO: (empty)`. `marker info m-22 --json` shows the payload is there: field `message` has
  `"value": "Call getUserMedia for {\"video\":{\"mediaSource\":\"screen\"}}"` but `"formattedValue": "(empty)"`; field `level` likewise.
- Cost: the test's own log, which the brief says to start from, was unreadable in the list; I had to read the task's live log to learn
  what the test was doing when it failed.
- Workaround: `marker info <handle> --json` per marker, or the job's `live_backing.log`.

## Default session directory not writable

- Command: `profiler-cli load <url> --session ...` without `PROFILER_CLI_SESSION_DIR`.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the message suggested `PROFILER_CLI_SESSION_DIR`, which worked.
- Minor: an environment issue more than a tool one, and the error said exactly what to do.
