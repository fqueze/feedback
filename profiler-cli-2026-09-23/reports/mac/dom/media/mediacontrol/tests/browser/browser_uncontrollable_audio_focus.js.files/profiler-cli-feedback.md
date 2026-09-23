# profiler-cli feedback

## Text marker list rounds times to whole seconds
- Command: `profiler-cli thread markers --session <s> --category Test --search browser_uncontrollable_audio_focus --list --limit 0`
- Expected: timestamps precise enough to order markers that are milliseconds apart (e.g. `t=66.2275s`).
- Got: `t=1m6s` for seven markers spanning 66.225s to 66.574s, and they were not listed in time order (m-11 before m-10 though both print 1m6s); `marker info` also prints `Time: 1m6s`.
- Workaround: `--json` and read `.flatMarkers[].start`.


## `load` selected a content thread as the default in a per-test mochitest profile
- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/NLppLoY-SCO3hMhdKG6rvg/runs/0/artifacts/public/test_info/profile_browser_uncontrollable_audio_focus-2.js.json --session <s>` then `thread markers --category Test --search <test> --list`
- Expected: the parent process GeckoMain (t-0) selected, as it was for the first profile of the same job.
- Got: `t-11 (GeckoMain, Privileged Content)` selected; the Test-category query returned "0 markers", which reads like the test log is missing rather than the wrong thread.
- Workaround: `thread select t-0`. A "0 markers" result on a thread that is not the parent main thread could say where Test markers are (`profile markers` would find them).


## (review) In which order did the receiving thread run two IPC messages?
- Command: `profiler-cli thread markers --session <s> --search "MediaAudibleChanged" --list --limit 0` on the parent main thread.
- Question: two content processes each send `PContent::Msg_NotifyMediaAudibleChanged`. Which one did the parent run first?
- Got: the list sorts and prints `IPCIn` markers by their start, which is the send time in the sender's clock, and the rows have no pid. With two senders 8 µs apart, the list order is the reverse of the order the parent ran them. The report being reviewed fell into this. Only `--json` gave `otherPid` and the end (the recv/dispatch time) to compare.
- Could show: for `IPCIn` rows, the other pid and the receive time in the default output (or sort by it). Also a note that send times come from another process's clock: in the same profiles, `IPCOut` markers with negative durations show about 0.1 ms of skew.
