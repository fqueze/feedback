## Log (INFO) marker fields print as "(empty)"

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session test_mouse_enterleave_iframe.html-1` (thread t-171, mochi.xorigin-test GeckoMain, profile of task Fz9drL9RRH-5Kd14iKhdkQ, `profile_test_mouse_enterleave_iframe.html.json`), and `profiler-cli marker info m-57`.
- Expected: the test's `info()` lines, e.g. `INFO  Mouse moves to initial position`.
- Got: every `INFO` row reads `[(empty)] INFO: (empty)`; `marker info` shows `Level: (empty)`, `Message: (empty)`. `marker info --json` has `"value": "Mouse moves to initial position"` but `"formattedValue": "(empty)"` for the `message` field (same for `level`).
- Question the default output should have answered: "what did the test log with info() just before it hung?" -- the test's own log, which the brief says to start from.
- Workaround: `marker info <handles> --json` and read `fields[].value`.

## Times past one minute print as `1m4s`, losing the milliseconds

- Command: `profiler-cli thread markers --search TEST-PASS --list --limit 0 --session test_mouse_enterleave_iframe.html-3` (task Tec8DkNhRPKSiEAZeCeA0A, the test ran at ~64 s).
- Expected: `t=64.253s`, as for times under a minute, so that events in different threads a few ms apart can be ordered.
- Got: every row `t=1m4s`; the whole test (load, three mouse moves, IPC) collapses onto one printed time.
- Question: "in which order did the iframe's first paint and the mousemove routing happen?" -- needs ms resolution.
- Workaround: `marker info <handles> --json` and read `start`, or `zoom push` with seconds (which accepts `64.19,64.30`).

## (review) Loading a large CI profile times out after 60 s by default

- Command: `profiler-cli load '<profiler.firefox.com from-url link to task Tec8DkNhRPKSiEAZeCeA0A profile_test_mouse_enterleave_iframe.html.json>' --session review-test_mouse_enterleave_iframe.html-3`
- Expected: the profile loads, as the two macOS ones did.
- Got: `Error: Profile load timeout after 60000ms (set PROFILER_CLI_LOAD_TIMEOUT_MS to override)`. A session was left behind that had to be stopped.
- Workaround: `PROFILER_CLI_LOAD_TIMEOUT_MS=400000`, then a reload. That cost one full download.

## (review) "Which IPC messages did the parent send to process X?"

- Command: `profiler-cli thread markers --search "otherPid:1784" --limit 0 --group-by messageType --session review-test_mouse_enterleave_iframe.html-1` (parent GeckoMain).
- Expected: one row per IPC `messageType`, with counts. The question was "did any mouse message go to the iframe's process?"
- Got: a single `(no value): 351 markers` group, even though `messageType` is a payload key of these markers in `--json`.
- Workaround: `--list --limit 0 --json`, then a Python script to count by `data.messageType`.
