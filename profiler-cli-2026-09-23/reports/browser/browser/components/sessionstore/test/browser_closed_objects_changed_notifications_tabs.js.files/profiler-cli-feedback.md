## When was this runnable enqueued? (browser_closed_objects_changed_notifications_tabs.js)

- Question: for a `Runnable` marker (`setTimeout() for #notifyOfClosedObjectsChange ... task: e1d76afbfa2472860`), when was that task queued, relative to another task that ran before it?
- Command: `profiler-cli thread markers --session S --search "e1d76afbfa2472860" --list --limit 0`
- Expected: the `TaskController::AddTask` / `EventQueueInternal::PutEvent` marker with the matching `flow=`, or a note that it was enqueued from an unprofiled thread.
- Got: 18 `Runnable` markers across the whole profile sharing that task id (the id is an address and gets recycled), and no enqueue marker (timer events are enqueued on the unprofiled Timer thread). Nothing says the id is recycled; it looks like one task running 18 times.
- Workaround: compared the order of Normal-priority runnables in the failing and a passing iteration instead. `marker info` on a Runnable could name its enqueue marker when one exists (nearest preceding AddTask/PutEvent with the same flow), and say "enqueued off-thread" otherwise.

## `thread samples-top-down --max-depth`

- Command: `profiler-cli thread samples-top-down --session S --include-idle --max-depth 4`
- Got: `error: unknown option '--max-depth'`. A depth limit is the usual way to keep a top-down tree readable; minor.

## Sub-millisecond order of markers (review of browser_closed_objects_changed_notifications_tabs.js)

- Question: was the PBackground reply sent before or after the TabClose event ended, and by how much? The two are 1.3 ms apart, and the race hinges on it.
- Command: `profiler-cli marker info m-22 m-64 --session S`
- Expected: start/end with microsecond precision, as the IPC payload's own `startTime` field has.
- Got: `Time: 26.664s - 26.672s`, and `t=26.664s` in `--list`: both markers print as the same millisecond.
- Workaround: `marker info --json` and a python one-liner to print `start`/`end`. Printing times to 0.1 ms (or µs) when a zoom range is under ~100 ms would answer it.
