## Question: in what order, to the millisecond, did these markers happen (within one test, across parent and inference process)?

- Command: `profiler-cli thread markers --session S --list --limit 0 --search "SendQuery,ReceiveQuery,Event::record,..."`
- Expected: a start time precise enough to order markers that are 0.1-1 ms apart.
- Got: every row prints `t=7m55s`: the whole race (about 20 ms, 30+ markers) collapses onto one timestamp, so the list cannot answer "did the engine-creation failure come before or after the third MLEngine:Removed". Same for `profile markers`.
- Workaround: `--json` piped into a python script printing `start/1000` with 4 decimals. Once the view is zoomed to under a few seconds, the default output could print seconds with ms (e.g. `475.3895s`), or relative to the zoom start.

## (review) `--session` before the subcommand is rejected

- Command: `profiler-cli --session browser-review-abort-1 status`
- Expected: the global option accepted anywhere, as `load ... --session X` suggests.
- Got: `error: unknown option '--session'`.
- Workaround: put `--session` after the subcommand.

## (review) Question: what IPC message is each IPCIn/IPCOut row, and when did the receiving process get it?

- Command: `profiler-cli thread markers --session S --list --limit 0` (zoomed to 30 ms, parent and inference threads)
- Expected: IPC rows labelled with their message type (`PMessagePort::Msg_ReceiveData`, `PContent::Msg_RawMessage`) and direction.
- Got: every row reads `IPCIn | IPCIn` or `IPCOut | IPCOut`. The type, direction and send/receive times are only in `--json` `data`, whose times use a different base from `start` (about 8 ms apart here).
- Workaround: `--json` and a python script, to pair each message's send and receive times and work out that the inference process clock ran about 1.0 ms ahead of the parent's. The row could print the message type and direction, and on the receiving side the time it was received on this thread's clock.
