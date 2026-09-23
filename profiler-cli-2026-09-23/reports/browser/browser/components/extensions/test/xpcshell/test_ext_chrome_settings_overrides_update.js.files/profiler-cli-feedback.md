## Question: in which order did markers within the same millisecond happen, and how far apart?

- Command: `profiler-cli thread markers --session S --list --limit 0 --search "..."` and `profiler-cli marker info m-479 m-810 ...`
- Expected: timestamps precise enough to order markers that are sub-millisecond apart (a test's `IOUtils.writeJSON` dispatch vs. a DeferredTask starting 0.7 ms later), and to see that a task ends 87 µs before a blocker is removed.
- Got: the list prints `t=2.983s` for a dozen markers spanning 2982.6 → 2983.7 ms; `marker info` prints `Time: 2.983s - 2.997s`. Row order in the list is not a reliable proxy (interval markers are ordered by start, and durations are rounded too).
- Workaround: `marker info ... --json` piped to a script printing `start`/`end` with 3 decimals of ms.
- What would have answered it: an option (or automatic behaviour when rows share a rounded time) to print times in ms with µs precision, e.g. `--precise-times`, in both `thread markers --list` and `marker info`.

## Question (review): in what order did markers less than 1 ms apart happen? (hit again)

- Command: `profiler-cli thread markers --session S --list --limit 0` after `zoom push 2.960,3.025`
- Expected: to see that a `TaskQueue::DispatchLocked` is followed by its `EventQueueInternal::PutEvent` about 5 µs later or has none, and whether a resolve came 84 µs before a task ended.
- Got: rows rounded to `t=2.983s` again, so the question in the entry above came back when reviewing. The output was also 32 KB for a 65 ms window, mostly `WindowProc`/`LogMessageRunnable`/`CONSOLE_MESSAGE` rows.
- Workaround: `--list --limit 0 --json` plus a script printing `start`/`end` to 3 decimals of ms and dropping the noise rows.
