## Default session directory not writable in a sandboxed agent

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster url> --session browser_ext_mousewheel_zoom.js-1`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, then "Unknown session" for the next command.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command (the error message suggested it). Falling back to `$TMPDIR` when the home directory is not writable would have saved the round trip.

## Question: "which tests ran in this browser, in order, with their status"

- Command: `profiler-cli thread markers --category Test --search "name:test" --list --limit 0`
- Expected: only the per-test `test` interval markers (`PASS — <path>`, `FAIL — <path>`).
- Got: every Text marker too, since `name` is also a payload key of Text markers (documented caveat), so ~1,500 rows.
- Workaround: `--search "browser/components/extensions/test/browser/" --min-duration 1`, which only works because the tests share a directory. A way to filter on the marker name only (e.g. `marker-name:test`) would answer it directly.

## Question: "which process was each of these IPC messages sent to"

- Command: `profiler-cli thread markers --search "PBrowser::Msg_Destroy,PBrowser::Msg_InitRendering,PBrowser::Msg_Show" --list --limit 0`
- Expected: one row per message with its type and direction ("sent to WebExtensions").
- Got: rows whose description column is empty for IPC markers — neither the message type nor the other process is printed, so the list cannot say which browser was created or destroyed in which process.
- Workaround: `--json` and a Python one-liner printing `fields.messageType` and `fields.recvThreadName`. `--group-by field:niceDirection` answers it for one message type at a time.
