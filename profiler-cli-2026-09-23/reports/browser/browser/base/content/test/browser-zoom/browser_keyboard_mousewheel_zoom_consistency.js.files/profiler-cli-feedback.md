## Which IPC messages did this thread send in a range (review, 2026-09-22)

- Command: `profiler-cli thread markers --search name:IPCOut --list --limit 0 --session browser-review-kbmw-zoom-1` (after `zoom push 20.075,25.2`)
- Question: did the content process ever send `PBrowser::Msg_WheelZoomChange` after the wheel, and what did it send instead?
- Expected: each IPCIn/IPCOut row labelled with its message type, as the `Runnable` rows are (`PVsync::Msg_Notify - priority: ...`).
- Got: IPC rows have an empty label (`m-18  IPCOut  t=20.007s  42.965ms  ✗`); the type is only in `marker info` or `--json` `data.messageType`. `--search WheelZoomChange` returning nothing only meant something once I had checked that `--search Msg_DidUnsuppressPainting` did match IPC markers.
- Workaround: `--json | python3` to count `data.messageType`.

## Which marker index a handle has, to check a profiler.firefox.com link (review, 2026-09-22)

- Command: `profiler-cli marker info m-527 --session browser-review-kbmw-zoom-1`
- Question: is this the marker the report's `marker=N` link points at?
- Expected: the marker index in the default output.
- Got: not shown; only `--json` has `markerIndex`. Checking 20 links took a loop over `--json` with python.
- Workaround: `marker info <m> --json | python3 -c '...markerIndex...'`.
