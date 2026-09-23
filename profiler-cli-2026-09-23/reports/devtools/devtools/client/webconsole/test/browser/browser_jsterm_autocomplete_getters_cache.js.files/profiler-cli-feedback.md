## Question: in what order did these markers happen within a 300 ms window?

- Command: `profiler-cli thread markers --search "RDP,setTimeout callback,Test,NotifyObservers" --list --limit 0 --session …` on a 76 s profile, zoomed to 74.20-74.64 s.
- Expected: start times I can order at the ms level. The diagnosis depended on a debounce timer runnable firing 50 µs after `watcher.target-destroyed-form` and 17 ms before `watcher.target-available-form`.
- Got: every row says `t=1m14s` or `t=1m15s`. Past one minute the column rounds to whole seconds, so the rows in a zoomed window cannot be ordered or spaced. The list also seems to come out in a different order than by start time (m-281 listed before m-286, m-228 before m-327, while the handles were not in time order).
- Workaround: `--json` and a Python script printing `start` and `duration` sorted by start. Showing ms (like `74.4978s`) whenever the view is shorter than a few seconds, or always inside a zoom, would have answered it.

## Question: when was function X on the stack?

- Commands: `profiler-cli thread functions --search clearCompletion` gives a sample count but no times. To place the calls I bisected with `zoom push 74.400,74.405` … `zoom pop` in a loop over eight ranges, calling `functions --search` each time.
- Expected: a way to list the sample times (or the first and last) whose stack contains a function, e.g. `thread samples --search clearCompletion --list` giving timestamps, or first/last sample times in `function info`.
- Workaround: the zoom bisection loop above, about 16 commands per function.

Correction to the first entry: the `--list` rows were in start-time order after all. The problem is only the whole-second rounding of `t=`.

## Question (review): when was this Runnable queued, and by which task?

- Command: `profiler-cli thread markers --search "AddTask,name:Runnable" --list --limit 0 --session review-…-1`, zoomed to 74.488-74.534.
- Expected: for each `TaskController::AddTask (flow=X)`, the Runnable whose `task: X` it queued, with its start. For a `Runnable`, when it was queued. This is how the review showed which task queued `NotifyUnhandledRejections`, and in what order the three RDP packets were queued.
- Got: two unrelated rows. The flow id is only in the AddTask label and the task id only in the Runnable label, so they have to be matched by eye or by script.
- Workaround: `--json`, plus a Python regex join on `flow=` / `task: `. A `queued at <t> (m-N)` column on Runnable rows, or `marker info` listing the linked AddTask, would have answered it.
