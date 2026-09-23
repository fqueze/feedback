## IPC markers in `thread markers --list` show no message type

- Question: "which IPC message is this IPCOut/IPCIn marker?" (to order `PWindowGlobal::Msg_SetSingleChannelId` against `PNecko::Msg_PDocumentChannelConstructor` in a content process).
- Command: `profiler-cli thread markers --search "SingleChannel,DocumentChannel" --list --limit 0 --session <s>` on a content-process thread.
- Expected: IPCOut/IPCIn rows labelled with `messageType` (and other pid), as the matching `Runnable` rows are.
- Got: `m-1323  IPCOut  t=6.802s  215.84μs  ✗` with an empty label. Searching for the message name does not match the IPCOut rows either, so only the receiving `Runnable` markers show up.
- Workaround: `thread markers --list --limit 0 --json` and a Python filter on `data.messageType` / `data.phase == "endpoint"`.
- The default output could show `messageType`, direction and other pid in the label of IPC markers.

## `thread network` truncates the URL at its end

- Question: "which of these requests is the page's `?sw-intercepted-resource` fetch, and which is the page itself?"
- Command: `profiler-cli thread network --sort start --limit 0 --session <s>`
- Got: `https://example.org/browser/browser/components/enterprisepolicies/tests/browser/sitepolicies_sw_f...`. The cut takes the end, which is where the discriminating query string was.
- Workaround: `--json` and print `url[-60:]`.
- Middle-eliding the URL (keep host and last path segment and query) would answer it.

## `data.startTime` in `--json` is on a different clock than the printed `t=`

- In `--json` output, `start` matches the printed `t=` but `data.startTime`/`data.endTime` of network and IPC markers are about 10 ms later (profile zero vs. process start?). Mixing them while building a timeline gave a spurious ordering until I noticed. Worth a note in the schema, or the same base for both.

## IPC phase times only as raw "no schema" fields (review-browser_policy_sitepolicies_serviceworkers.js.bug2064102)

- Question: "had the parent's I/O thread already received this IPC message when that other runnable ran?" (in transit, or queued on the receiving thread?)
- Command: `profiler-cli marker info m-10 --session <s>` on an IPCIn marker.
- Expected: the phases the profiler tooltip derives (Send Thread Latency, IPC Send Duration, IPC Recv Latency, Recv Thread Latency), or the phase timestamps on the printed `t=` clock.
- Got: `sendStartTime`, `sendEndTime` and `recvEndTime` under "Other payload fields (no schema)", on the raw clock (about 10 ms, 7.5 ms and 6.8 ms off `t=` in three profiles). I had to derive the offset per profile from `start` minus `data.startTime`.
- Workaround: `--json` for each profile, computing the offset, then `recvEndTime - offset`.
- `marker info` for IPC markers could print the four latencies and the phase times on the `t=` clock.
