# profiler-cli feedback (browser_policy_sitepolicies_serviceworkers.js.bfcache)

## Question: "which process sent each IPC message of type X, in time order, on this thread"

- Command: `profiler-cli thread markers --session S --list --limit 0 --search "SetSingleChannelId"`
- Expected: the IPCIn/IPCOut markers of that message type, with their message type and sender (`From: https://example.org (2/5)`) on each row.
- Got: the search matches the `Runnable` rows ("PWindowGlobal::Msg_SetSingleChannelId - priority ..."), and the `IPCIn` rows print with an empty description. Message type and sender are only in `marker info` or `--json` (`data.messageType`, `data.sendThreadName`).
- Workaround: a script over `thread markers --search name:IPCIn --json` filtering on `data.messageType` and printing `sendThreadName` (`ipc.py` in this directory). The race diagnosed here hinged on the interleaving of two message types from one content process with a parent-side callback; the default list could have shown it if IPC rows carried "Type — from/to" as their description.

## Searching for text the list itself prints does not find the marker

- Command: `profiler-cli thread markers --session S --search "bfcache.combo" --list`
- Expected: the `Counter::add` markers that the list prints as `bfcache.combo BFCache_Success: 1`.
- Got: "No markers match". The printed text is built from the fields `cat=bfcache`, `id=combo`, `label=BFCache_Success`; searching `BFCache_Success` works.
- Suggestion: also match the tooltip label / printed description, so copying text from an earlier output finds the marker again.

## (review) Question: "when did the recipient run this IPC message, and when was it sent"

- Command: `profiler-cli thread markers --session S --list --limit 0` (zoomed), then reading an `IPCIn` row.
- Expected: an `IPCIn` row on the receiving thread, at the time the message was received or run there.
- Got: the row is placed at the sender's send time, and its duration runs to the recipient's dispatch. Nothing in the row says so. The report under review read two `IPCIn` rows at 5.306 as "arrive at 5.306", but one of them only ran on the parent 1.9 ms later. Finding out took `marker info --json` (`data.startTime`/`endTime`, `phase=endpoint`) and matching each row with its `Runnable`.
- Suggestion: print IPC rows as "<type> sent <t1> → run <t2> (from <process>)". Say when the transfer phases are missing, because the I/O threads were not profiled.

## (review) Question: "which marker does this link's marker=N select"

- Command: `profiler-cli load '<url>?marker=74801&thread=0'`, then `status`.
- Expected: the load, or `status`, names the selected marker as `m-…`.
- Got: only the thread and the range. Finding marker N took a zoom and a `--search`, then `marker info --json` on each candidate to compare `markerIndex`. In this report, several links turned out to be `Runnable` markers and not the `IPCIn` markers the text suggested.
- Suggestion: report the selected marker on load, or accept `marker info --index N`.
