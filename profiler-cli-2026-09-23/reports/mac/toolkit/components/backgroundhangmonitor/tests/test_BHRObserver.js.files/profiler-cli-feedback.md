## Question: "what was the machine's CPU use while this test ran?" (resource-usage profile)

- Command: `profiler-cli zoom push m-1 --session test_BHRObserver.js-3` then
  `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session test_BHRObserver.js-3`
- Expected: each `CPU Use` row to show its `cpuPercent` (the only thing in it worth reading), or an
  aggregate (mean/min/max of cpuPercent over the zoom).
- Got: 410 rows of `CPU Use  t=4m45s  99ms  ✗` with no value at all.
- Workaround: `--json` piped into a script bucketing `data.cpuPercent` per 2 s.
- What the default output could have shown: the payload's cpuPercent per row, or a one-line
  "CPU Use over view: mean 100%, min 100%" summary when every listed marker has the same numeric field.

## Profile that fails to load: two processes wrote the same file

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/SgvtLGZKSZS0SCWu7Q7IoQ/runs/0/artifacts/public/test_info/profile_test_BHRObserver.js.json --session ...`
  (also both `profile_test_BHRObserver_retry.js.json` of tasks SgvtLGZKSZS0SCWu7Q7IoQ and WgINS7cnSseqypcayKXhjA)
- Expected: a loaded profile, or an error saying why the file is unusable.
- Got: `Unserializing the profile failed: SyntaxError: Expected ',' or ']' after array element in JSON at position 405495`
  / `Unexpected "." at chunk position "368642" ... in state START`.
- Workaround: downloaded the file and read the bytes: it starts with the child process's
  (`plugin-container`) libs, with the parent's JSON interleaved into it. Root cause is the harness,
  not profiler-cli: xpcshell `head.js` `_execute_test` calls `profiler.scheduleDumpToFile` with
  `MOZ_TEST_TIMEOUT_PROFILE_PATH` without a `runningInParent` guard, so a `run_test_in_child` test's
  child dumps to the same path as the parent when the timeout hits.
- What the error could have shown: "the JSON is malformed at byte N; the file starts with a profile
  of process `plugin-container`" — i.e. the first lib name / pid, which points straight at the collision.

## A failed load leaves its daemon running

- Command: `profiler-cli load <corrupted profile URL> --session test_BHRObserver.js-1` → `Error: Profile load failed: ...`
- Expected: no session left behind when the load fails (or the error to say the session still exists).
- Got: `profiler-cli session list` still showed `test_BHRObserver.js-1` and `test_BHRObserver.js-4` with live daemon pids
  20 minutes later, each counting against the "at most two loaded" budget.
- Workaround: `profiler-cli stop <id>` for each.
