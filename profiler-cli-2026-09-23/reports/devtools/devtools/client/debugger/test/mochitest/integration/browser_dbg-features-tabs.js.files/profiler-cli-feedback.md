# profiler-cli feedback (browser_dbg-features-tabs.js)

## `load` selected a non-parent thread, and a later Test-category query silently returned 0
- Command: `profiler-cli load https://…/cF-TjWktQkCBcg332Myiqw/…/profile_browser_dbg-features-tabs.js.json --session …-4`, then `thread markers --category Test --search browser_dbg-features-tabs --list --limit 0`
- Expected: the parent process main thread (t-0) selected by default, as in the other profiles, or at least the header line showing which thread the query ran on.
- Got: t-10 (GeckoMain, WebExtensions) was selected. The `--json` query returned an empty list with no hint that it ran on the wrong thread.
- Workaround: `thread select t-0`.

## Text marker lists round times to 1 s in long profiles
- Question: "In what order did the content main thread and a new DOM Worker act within 2 ms (worker wakes, main wakes, IPC sent)?"
- Command: `thread markers --list` on t-83 and t-86 of a 7-minute profile. Every row reads `t=6m37s`.
- Expected: sub-millisecond times, relative to a zoom or a marker, so two threads can be ordered at that scale.
- Got: 1 s resolution, so the order cannot be read. Workaround: dump `--json` for each thread and merge-sort the markers in a Python script (start + duration, filtered).
- Related trap: `--json` `start` is in ms, but `zoom push` takes seconds. My first `zoom push 396548.0,396551.0` (ms) produced output with no error. It was not the window I meant.

## IPC markers do not show their message type in text output
- Question: "Did this content process send `PRemoteWorkerDebuggerManager::Msg_Register` for this worker?"
- Command: `thread markers --search Msg_Register --list`. It matches, but each row's label is just `IPCOut` / `IPCIn`.
- Expected: the `messageType` (and direction) in the row label.
- Workaround: `--json` and print `data.messageType`.
