## Question: when did this IPC message reach the receiving thread's queue?

- Command: `profiler-cli marker info m-1040 --session jstracer-1` (an `IPCIn` marker, `PContent::Msg_RawMessage`).
- Expected: the send, receive (IO thread) and processing times, on the same time axis as every other marker (`t=30.165s`).
- Got: `Time: 30.165s - 30.166s` (send start to processing end), with `sendStartTime`, `recvEndTime`, `startTime`, `endTime` only as raw absolute milliseconds (`30173.153...`) under "Other payload fields (no schema)", whose zero differs from the displayed times by ~8 ms (and by a different amount in another profile, ~7.4 ms).
- Workaround: computed the offset from `startTime` vs the displayed start, then converted `recvEndTime` by hand. Showing each phase as `t=` relative times would have answered it directly; I needed it to bound when a timer event could have been queued (FIFO argument).

## Default selected thread after `load` is sometimes a content process

- Command: `profiler-cli load <per-test profile URL> --session jstracer-2` then `thread markers --category Test --list`.
- Expected: the parent process main thread selected (as for the first profile I loaded, t-0), since that is where the test log is.
- Got: `t-18 (GeckoMain, Privileged Content)` selected, and "No markers match", which reads like the test logged nothing. Same for a third profile (t-53).
- Workaround: `profile info --search GeckoMain`, then `thread select t-0`.

## (review) A search term containing ": " silently becomes a field filter and matches nothing

- Command: `profiler-cli thread markers --session review-browser_dbg-javascript-tracer.js-1 --search "priority: Idle" --list --limit 0`, zoomed to 29.95–30.211s.
- Expected: the `Runnable` markers whose name contains `priority: Idle`, or an error saying `priority` is not a field.
- Got: `0 markers … No markers match the specified filters.` A bare `--search Idle` over the same range listed five `… - priority: Idle (0)` runnables. The empty result almost became a claim that no idle-priority task ran.
- Workaround: searched `Idle` and filtered the output with `rg`. A warning like "`priority` is not a field of any marker in view, so the term was treated as field:value" would have caught it.

## (review) Question: which marker handle is the one a link's `marker=N` selects?

- Command: `profiler-cli load "<profiler link with marker=429777>" --session …`, then `thread markers …`.
- Expected: `load` or `status` naming the selected marker's handle, e.g. `Selected marker: m-445 (index 429777)`, or `marker info --index 429777`.
- Got: no mention of the link's marker. To check a report's link, I looped `marker info m-N --json` over handle ranges until `markerIndex` matched (for 429777 and 384902).
- Workaround: shell loops over `--json`.
