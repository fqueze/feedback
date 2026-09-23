## Question: "what was this 0.25 ms idle runnable?" (a `Runnable` marker too short to be sampled)

- Command: `profiler-cli zoom push 5.1265,5.1285 --session S; profiler-cli thread markers --session S --list --limit 60`
- Expected: the markers that happened inside that 2 ms window, to see what the runnable did.
- Got: the list starts with every marker *overlapping* the window, including ones that began seconds
  earlier (`AsyncShutdown blocker` from t=1.27s, `browserFirstWindowReady`, dozens of long `IPCOut`/`IPCIn`
  pairs), so the 2 ms of interest were below the fold. No option to keep only markers starting in the range.
- Workaround: `--search "-name:IPCOut,-name:IPCIn"`, then gave up on content and matched the runnable's
  `Task` field against `TaskController::AddTask (flow=...)` markers to learn when it was queued. A
  "queued at" link from a `Runnable` marker to its `AddTask` marker would have answered it directly.

## Question: "which idle-priority runnables ran after t=1.3s?" (review-browser_startup_timeline_telemetry.js)

- Command: `profiler-cli thread markers --session S --search "priority: Idle" --list --limit 0`, then `--search "Idle (0)"`
- Expected: the `Runnable` markers whose row reads `... - priority: Idle (0) task: ...`.
- Got: 0 markers for both, although the row text contains those strings. The printed label is built from fields; `priority` is the number 0 and the name is in a separate `priorityName` field.
- Workaround: `marker info --json` to find the key, then `--search priorityName:Idle`. Matching bare terms against the printed row text, or listing the searchable field keys in `thread markers --help` output for common marker types, would have answered it at once.

## Question: "was this AddTask before or after that Runnable with the same flow id?"

- Command: `profiler-cli thread markers --session S --search <flow id> --list --limit 0`
- Expected: enough precision to order an `AddTask` and a `Runnable` that share a flow id (task addresses are reused).
- Got: the list sorts by start time, but prints it rounded to 1 ms (`t=4.209s` for all of them). Reused ids made `AddTask` / `Runnable` pairs within the same millisecond ambiguous.
- Workaround: `marker info m-a m-b ... --json` for the exact `start`. More digits in `--list` rows when neighbouring rows share the rounded time would have answered it.
