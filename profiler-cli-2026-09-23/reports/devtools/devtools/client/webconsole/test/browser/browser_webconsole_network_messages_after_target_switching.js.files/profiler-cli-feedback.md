## Ordering markers within the same millisecond (browser_webconsole_network_messages_after_target_switching.js)

- Question: in which order did "Leaving test task", the setTimeout callback, the harness runnable, the actor call and the microtask checkpoint happen? They all print as `t=9.220s` / `t=9.226s`.
- Command: `profiler-cli thread markers --list --limit 0 --search ... --session S`
- Expected: a way to get sub-ms start/end in the list (e.g. `--precise-times`, or more digits when neighbouring rows share a millisecond).
- Got: millisecond-rounded times only; I had to loop over `profiler-cli marker info m-N --json` and read `.start`/`.end` in a script.
- Also: `marker info --json` has no `handle` field in its record (printed None), so a batch `marker info m-1 m-2 --json` cannot be matched back to handles without relying on order.

## Sample count vs call tree (same profile)

- Command: `profiler-cli thread info` says "This thread contains 229 samples"; `thread samples-top-down --include-idle` on the full range shows percentages in steps of 16.7%, i.e. 6 samples.
- Expected: the call tree over the full range to account for the 229 samples, or a line saying how many samples it is built from and why the others are excluded.
- Got: no explanation; I could not tell whether samples were dropped or the count was wrong.

## Following a runnable from its AddTask

- Question: which runnable delivered the packet queued at time T?
- Worked with `thread markers --search <flow id> --list`, matching `TaskController::AddTask (flow=X)` to `Runnable ... task: X`. Worth documenting in the guide: it is the only way to link a DevTools executeSoon dispatch to the runnable that later runs it. Flow ids are addresses and get reused, so the search returns unrelated pairs too.
