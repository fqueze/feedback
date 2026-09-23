## Question: what else failed in this job, including shutdown hangs and their profiles?

- Command: `fx-tests task e7OWzSu9TZuu2o2YgQY0YQ --profiles` (and `--messages`).
- Expected: the job's `TEST-UNEXPECTED-FAIL | shutdown hang | profile uploaded in profile_shutdown_hang_1622.json`, with its profile URL. That profile was the key one here: it covers the whole browser session, and it is the only one showing the Renderer never recovered.
- Got: only the 4 test failures and their per-test profiles. Neither the shutdown hang nor `profile_shutdown_hang_1622.json` is mentioned.
- Workaround: found the `output` marker in the resource-usage profile, then listed the task's artifacts with curl.
- What the output could have shown: a "shutdown / harness failures" section listing the shutdown hang and its profile URL.
