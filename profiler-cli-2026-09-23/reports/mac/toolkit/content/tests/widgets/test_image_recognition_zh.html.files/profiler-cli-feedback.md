## Question: the exact code points in a marker's message, which contains invisible C1 control characters

- Command: `profiler-cli marker info m-23 --session irzh-1` on
  https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/BtPA1kUTTCmM0I-U8wW9BQ/runs/0/artifacts/public/test_info/profile_test_image_recognition_zh.html.json
  (thread t-18, TEST-UNEXPECTED-FAIL `The Chinese characters for Firefox are found. - got "火狐", expected "..."`).
- Expected: a way to see that the expected string has 6 code points, U+00E7 U+0081 U+00AB U+00E7 U+2039 U+0090,
  and not the 4 the terminal displays. Those 6 code points are what proves the test file was decoded as windows-1252.
- Got: the text output and the `--json` output both print the C1 controls (U+0081, U+0090) raw, so the terminal drops
  them.
- Workaround: pipe `--json` into a Python script that prints `hex(ord(c))` for each character of the `message` field.
- What the output could have shown: non-printable characters escaped (``) in the text output, or a flag to
  turn that on.

## Sandbox: the default session directory is not writable

- Command: `PROFILER_CLI_SESSION_OWNER=test_image_recognition_zh.html profiler-cli load <taskcluster url> --session ...`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error message suggested
  `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`, and that worked. It cost one retry, and every
  later command needs the variable too.

## Question: a network marker's content type and status (review-test_image_recognition_zh.html)

- Command: `profiler-cli marker info m-11 --json --session review-test_image_recognition_zh.html-1` on
  the same profile (thread t-5, `Load 2147483673: http://mochi.test:8888/.../test_image_recognition_zh.html?...`).
- Expected: `contentType`, `responseStatus` and `requestMethod` under `fields`, where they are for Test markers.
- Got: `"fields": []`. The values were only under `rawFields`, so the first script printed nothing, and it cost a second
  call to dump the whole JSON.
- Workaround: read `rawFields`.
- What the output could have shown: the network payload under `fields` too, or a note that `fields` is empty
  because this marker type's schema lists none.
