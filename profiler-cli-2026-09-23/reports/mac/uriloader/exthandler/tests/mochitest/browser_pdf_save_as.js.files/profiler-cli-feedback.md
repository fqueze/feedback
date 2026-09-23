## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=browser_pdf_save_as.js profiler-cli load <taskcluster URL> --session browser_pdf_save_as.js-1`
- Expected: session loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the message did suggest `PROFILER_CLI_SESSION_DIR`, which worked.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call. Cost one call; falling back to `$TMPDIR` when the home default is not writable would avoid it (the brief could also name the variable).

## (review) IPC rows in `--list` do not say which message they are

- Question: "when did the parent send `PBrowser::Msg_UpdateDimensions`, relative to the `Test:SynthesizeMouse` SendQuery?"
- Command: `profiler-cli thread markers --search UpdateDimensions --list --limit 0 --session review-browser_pdf_save_as.js-1` on the parent main thread
- Expected: a row naming the message, e.g. `IPCOut t=31.862s 1.724ms PBrowser::Msg_UpdateDimensions → Web Content (9/10)`.
- Got: `m-863  IPCOut  t=31.862s  1.724ms  ✗` with an empty description, next to dozens of other unnamed IPCOut rows; only `marker info` shows the type. Its send time there is under "Other payload fields" as `sendStartTime: 31870.084375`, absolute milliseconds, not the `t=` seconds used everywhere else, so matching it to the content side meant subtracting the offset by hand.
- Workaround: `marker info` on each row. Putting the message type and peer in the IPC row's description, and printing the send/receive times as `t=`, would have answered it from the list.
