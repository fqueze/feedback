## Question: which IPC message was the slow one?

- Command: `profiler-cli thread markers --session <s> --thread t-15 --category IPC --list --limit 0` (zoomed on 12.3–12.9 s)
- Expected: each IPCIn/IPCOut row to name its message type (e.g. `PWindowGlobal::Msg_Destroy`), since that is what an IPC row is about.
- Got: rows like `m-2559  IPCOut  t=12.326s  390.85ms  ✗` with an empty description, so 160 rows of identical-looking IPCOut lines. I had to loop `marker info <h> --json` over 25 handles in a script to find the one that mattered.
- Workaround: `--search "PWindowGlobal::Msg_Destroy"` works once you already know the type; otherwise a script over `marker info --json`.
- What would have answered it: show `messageType` (and maybe direction/other pid) as the row description for IPC markers in `--list`.

## Question: how many objects did each CC free, over time?

- Command: `profiler-cli thread markers --session <s> --thread t-15 --category "GC / CC" --list --limit 0`
- Expected: CC rows to show the headline fields (visited / freed refcounted and GC objects), as the Firefox Profiler tooltip does.
- Got: `m-328  CC  t=12.364s  31.164ms  ✗` with no description. Comparing 30 CCs meant a script over `marker info <h> --json` reading `mVisitedGCed`, `mFreedRefCounted`, `mFreedGCed`.
- What would have answered it: a short label for CC markers in `--list`, e.g. `visited 533 RC / 2,495 GC, freed 0 / 0`.

## `--search name:CC` matches unrelated markers

- Command: `profiler-cli thread markers --thread t-15 --search name:CC --list --json`
- Expected: the CC markers. Got: 1,947 markers (Telemetry, GetService...), because `name` is also a payload key. The guide does warn about it; still cost a round trip. Workaround: `--category "GC / CC"` and grep the name column.

## Question: when did the other process receive this IPC message? (review-test_event_listener_leaks.html)

- Command: `profiler-cli marker info m-45 --session <s> --json`, reading `rawFields` `recvEndTime` next to `start`/`end`.
- Expected: every time in one marker to use the same zero.
- Got: `start`/`end` use profile-relative time, but `rawFields` times (`startTime`, `sendStartTime`, `recvEndTime`) do not. The offset from `start` was 10.2 ms in one profile, 0.3 ms in another and 7 ms in a third. Read at face value, `recvEndTime` looks like a 10 ms transport delay that did not happen. I had to subtract the raw `startTime` myself.
- What would have answered it: show send/recv/handled times as profile-relative fields, or IPC phase durations (send → IO-thread receive → handled), in `marker info`.

## `marker info` with several handles returns another shape

- Command: `profiler-cli marker info m-1 m-2 --json`. Expected an array, or the same object as for one handle. Got `{markers: [...], errors, requested}`, while a single handle returns the bare object. My first jq failed. The guide says "one record per handle" but not that the wrapper changes.
