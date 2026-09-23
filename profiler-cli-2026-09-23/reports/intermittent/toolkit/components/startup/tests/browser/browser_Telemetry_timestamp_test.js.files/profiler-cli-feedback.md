## `zoom push` with minute-style times is silently misread

- Command: `profiler-cli zoom push 15m0s,21m30s --session <id>` on a 30m55s resource-usage profile
- Expected: a zoom from 15 min to 21.5 min (the same format `thread markers` prints its times in), or an error.
- Got: `Pushed view range: ts-1 (15s) to ts-2 (21s)`, a 6 s range starting at 15 s.
- Workaround: pass seconds (`900,1290`).

## Question: how busy was the main thread in this range, and where were its idle gaps? (review)

- Command: `profiler-cli zoom push 1.215,1.46` then `thread samples --include-idle` and `thread markers --search Runnable`
- Expected: the fraction of wall time the thread was busy in the range, and its longest idle gaps (to judge whether idle tasks had any chance to run).
- Got: 6 samples in 245 ms (5 active), which is too few to use. The marker summary gives count and average duration, but not the union of their time, and nested runnables and `Perform microtasks` outside any runnable make the sum misleading. The text list prints start times at 1 ms precision only.
- Workaround: `thread markers --search "Runnable,Perform microtasks,GCSlice,CCSlice" --list --limit 0 --json` and a script that merges the intervals. The result was 179 of 230 ms busy, with gaps of 10 to 15 ms.
