## `thread markers --list` rounds times to the second once past 60 s

- Question: when did each step of the test happen, to the millisecond?
- Command: `profiler-cli thread markers --category Test --search browser_ext_webNavigation_onCreatedNavigationTarget.js --list --limit 0 --session onCNT-1`
- Got: every one of the test's 56 markers at `t=6m22s` or `t=6m23s`, so its 0.8 s body and the 7.9 s vsync wait could not be told apart. `marker info` prints the same rounded `Time: 6m22s`.
- Workaround: `--json` and a script printing `start/1000` to 3 decimals.
- Could have shown: `t=382.759s` (or `6m22.759s`), the same precision as under 60 s.

## Which threads got a marker in a window, and how many

- Question: during the test's vsync wait, which processes received `PVsync::Msg_Notify`, how many times, from when to when?
- Command: `profiler-cli profile markers --search "PVsync::Msg_Notify" --session onCNT-1` (after `zoom push`)
- Got: "1419 markers across 3 of 33 threads", then an interleaved list of the first markers; no per-thread counts.
- Workaround: `--json`, group `markers[]` by `threadHandle` and `name` in a script.
- Could have shown: a per-thread summary (thread, count, first, last) before the list, e.g. `t-32 WebExtensions: 472 IPCIn, 382.769..390.619`.

## (review) A session name derived from a long report name exceeds the socket path limit

- Question: load a profile under the session name the review brief asks for, `review-<report name>-1`.
- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli profiler-cli load <link> --session review-browser_ext_webNavigation_onCreatedNavigationTarget.js-1`
- Got: `The Unix socket path for this session is 109 bytes, over this platform's 103-byte limit`. The error is clear, but the name is the one the brief prescribes, and it cost a retry.
- Workaround: a short session name (`rv-onCNT-1`), keeping the full name in `PROFILER_CLI_SESSION_OWNER`.
- Could have: derived the socket file name from a hash of the session id, so any session name works.
