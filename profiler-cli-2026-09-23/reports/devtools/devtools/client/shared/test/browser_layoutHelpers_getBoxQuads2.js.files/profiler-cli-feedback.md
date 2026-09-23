## Sub-second marker times lost past one minute (`thread markers --list`)

- Command: `profiler-cli thread markers --category Test --search browser_layoutHelpers_getBoxQuads2 --list --limit 0 --session browser_layoutHelpers_getBoxQuads2.js-3` on a 1m45s mochitest per-test profile (task OsQWrmPOTdip82mvMp5D2w).
- Expected: the times as `t=104.588s` (or `1m44.588s`), as the list prints them under one minute.
- Got: every row `t=1m44s` / `t=1m45s`: the whole test, 360 ms, reads as two instants, and the order of events cannot be read.
- Workaround: `--json` and a script printing `start/1000`. Question it could not answer: "in what order, to the millisecond, did these markers happen in a profile longer than a minute".

## IPC markers show no message type in `--list`

- Command: `profiler-cli thread markers --search IPCOut --list --limit 0 --session browser_layoutHelpers_getBoxQuads2.js-1` (content process t-9, task Xi9__pyfSKyCLqNSvZu5_Q).
- Expected: the message type (`PWebRenderBridge::Msg_SetDisplayList`, ...) in the description column, like the `Runnable` rows do.
- Got: `IPCOut  t=2.583s  -106,246ns  ✗` with an empty description, for every IPCIn/IPCOut row; also negative durations.
- Workaround: `--json` and a script over `fields[].messageType`. Question: "which IPC messages did this process send in this window".

## Which document a marker belongs to (review-browser_layoutHelpers_getBoxQuads2.js)

- Question: "which of the three documents in this content process (A, C, E) fired this `load` / ran this `RefreshDriverTick` / got this `visibilitychange`", to tell whose paint reached the compositor.
- Command: `profiler-cli thread markers --search DOMEvent --list --limit 0` and `--search RefreshDriverTick` on t-9 (task Xi9__pyfSKyCLqNSvZu5_Q). Then `--search innerWindowID:472` on t-105 (task OsQWrmPOTdip82mvMp5D2w).
- Expected: `innerWindowID` in the list rows, or at least usable as a `field:value` search.
- Got: it appears only in `marker info` under "Other payload fields (no schema)", as `rawFields` in JSON. `--search innerWindowID:<id>` matches nothing, and a bare `<id>` matches unrelated pref values.
- Workaround: a script calling `marker info --json` on each handle to map it to its window id.
- Separately, `--search eventType:load` also matches `beforeunload`. The substring match on a field value is surprising for enum-like fields.
