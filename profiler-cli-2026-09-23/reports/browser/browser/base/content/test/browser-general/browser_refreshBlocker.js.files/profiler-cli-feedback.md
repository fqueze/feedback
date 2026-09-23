## Question: in what order, and how many ms apart, did these markers happen (on one thread, and across a content and the parent thread)?

- Command: `profiler-cli thread markers --search "RefreshBlocker,name:SpecialPowers,category:Test" --list --limit 0 --session S`
- Expected: a start time per row precise enough to order events a fraction of a millisecond apart (the race here is 0.3 ms wide).
- Got: every row says `t=1m28s`; `marker info` also says `Time: 1m28s (instant)`. Once zoomed into a 4 ms range the rows still print `t=1m28s`.
- Workaround: `--json` piped into a 5-line script printing `start` with 3 decimals. Needed for every timeline in this report (about 25 calls). What would have answered it: print ms (or relative-to-zoom-start ms) when the view range is short, or a `--precise-times` flag.

## IPC marker with a negative duration and a start that disagrees with its own payload

- Command: `profiler-cli marker info m-10917 --session S` (IPCOut `PBrowser::Msg_OnLocationChange`, content thread t-114, BKZ09-eLTmuOMEfcb8gTtw per-test profile)
- Expected: a send time in the same timebase as the other markers of the thread, and a non-negative duration.
- Got: `Time: 1m22s - 1m22s (-1,102,916ns)`; the list shows start 82312.810, while the payload says `startTime: 82320.387`, `recvEndTime: 82319.2334` (7.58 ms apart from the listed start). The matching parent-side IPCIn is listed at 82312.81 too, but the parent's `PBrowser::Msg_OnLocationChange` runnable ran at 82311.707. Every first `OnLocationChange` of a new content process has `messageSeqno: -42`, so the pairing may be matching the wrong message.
- Workaround: used the IPCOut's listed start only as a position between the content thread's own DOMWindowCreated and DOMDocElementInserted markers, and did not use its cross-process timing.

## (review) Question: which thread does a link's `thread=zk` open?

- Command: `profiler-cli load "<profiler.firefox.com link with thread=0>" --session S`, then checking the report's other links on the same profile (`thread=zk`, `thread=zj`, `thread=e`).
- Expected: a way to map a URL thread param to a handle without reloading, e.g. a URL-param column in `thread list`.
- Got: only the loaded link's own thread is reported. Also, a session loaded from a profiler.firefox.com link records that link as its `profilePath`, so `profile-link.py` refuses to build links from it. Adding linked observations meant loading the same profile again from the Taskcluster URL (about a minute, 0.6 GB).
- Workaround: decoded the params by hand with `profile-link.py`'s `encode_threads` (116 is "zk").

## (review) Question: when did this IPC message reach the receiving thread's queue?

- Command: `profiler-cli marker info m-10 --session S` (IPCIn `PWindowGlobal::Msg_RequestDocumentLanguageMetadata`, t-114, BKZ09-eLTmuOMEfcb8gTtw per-test profile)
- Expected: the IO-thread receive time, in the thread's timebase and labelled as such.
- Got: start 82313.794 and end 82322.967 (the end is the runnable). Nothing says whether the start is the sender's send time, in the other process's clock, or the local receive time. On t-114, the payload's `startTime`/`sendStartTime`/`recvEndTime` are all offset from the listed times by a constant 7.577 ms, so they cannot be compared with the thread's other markers either.
- Workaround: did not use IPCIn starts as evidence. Only used the order of `TaskController::AddTask` markers and runnables on the same thread.
