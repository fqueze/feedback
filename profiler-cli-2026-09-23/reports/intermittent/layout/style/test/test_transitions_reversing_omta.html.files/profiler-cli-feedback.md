## Question: in what order, to the millisecond, did things happen in a 0.5 s window?

- Command: `profiler-cli thread markers --min-duration 20 --list --limit 0 --session <s>` (and the same with `--search "CSS transition,RefreshDriverTick,TEST-,CCSlice,..." --list`), zoomed to a 3 s window.
- Expected: a start time precise enough to order markers that are milliseconds apart (a transition ending at 504.141 vs a sync IPC at 504.148 vs a TEST-UNEXPECTED-FAIL at 504.158).
- Got: every row shows `t=8m24s` (second resolution), so the order and gaps within the window were unreadable; also the list is not sorted by start when the markers overlap.
- Workaround: `--json` and a Python script printing `start/1000` with 3 decimals, sorted by start.
- What would have answered it: a `t=` column with ms precision when the view is zoomed under a few seconds (or a `--time-format ms` flag).

## Question: how busy was the machine during a 400 ms window (resource-usage profile)?

- Command: `profiler-cli thread markers --search "name:CPU Use" --list` on a zoomed resource-usage profile.
- Expected: the CPU percentage per 100 ms sample in the list.
- Got: rows labelled only `CPU Use`; `counter list` says "No counters in this profile".
- Workaround: `--json`, reading `fields[].cpuPercent` / `idle_pct`.
- What would have answered it: the `cpuPercent` value in the row label, or exposing the CPU Use markers as a counter so `counter info` works.

## IPC marker start vs payload startTime differ by ~10 ms

- Command: `profiler-cli thread markers --search GetAnimationValue --list --json` on t-16 and on the Compositor.
- Got: `start` 504147.9 while `data.startTime` is 504158.09 for the same SyncIPCOut, on both sides. Other markers on the same thread (TEST-UNEXPECTED-FAIL) are at 504158.
- Expected: either both in the same time base, or a note saying which one to trust. It cost time to work out whether the sync IPC came before or after the transition end.

## (review) Question: every marker in a 0.5 s window, in order

- Command: `profiler-cli thread markers --min-duration 0 --list --limit 0 --json --session <s>`, zoomed to 503.7,504.2 on the content main thread.
- Expected: `--min-duration 0` to be a no-op, so every marker in the window.
- Got: 123 markers instead of 210. All 87 instant markers (duration null) were silently dropped, including the `TEST-PASS` / `TEST-UNEXPECTED-FAIL` I was looking for; nothing said so.
- Workaround: drop `--min-duration`.
- What would have answered it: keep instant markers at `--min-duration 0`, or print "N instant markers excluded".

## (review) profile-link.py refuses a session loaded from a profiler link

- Command: `profiler-cli load "https://profiler.firefox.com/from-url/<artifact>/marker-table/?marker=N&thread=g&v=17" --session <s>` (what review-brief.md says to do to check a link), then `profile-link.py --session <s> --marker m-450`.
- Expected: a link to that marker, since the session knows the artifact URL inside the from-url link.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself". I had to reload the 0.6 GB profile in a second session to build one link.
- What would have answered it: profile-link.py unwrapping the `from-url/` artifact URL, which it can decode from the loaded link.
