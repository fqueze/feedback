## `--search "Idle (0)"` misses markers whose name contains it

- Command: `profiler-cli thread markers --search "TimeoutExecutor,Idle (0)" --list --limit 0` with a zoom of 25.10,25.31 on profile YjE5-vBuQYSd6mMXi16-qg (worker test).
- Expected: the `TimeoutExecutor Runnable` markers plus the two `IdleRequestExecutor - priority: Idle (0) ...` runnables at t=25.297s, which are in that range.
- Got: only the two `TimeoutExecutor` markers. `--search IdleRequestExecutor` found the idle ones fine.
- Workaround: search on a term without spaces or parentheses.

## Question: "when did the task queued at time X run?"

- Question: for each `requestIdleCallback` made by `Toolbox.open()`, when did its `IdleRequestExecutor` run. The link is the `TaskController::AddTask (flow=...)` id matching the `Runnable ... task: <id>`.
- Commands: zoom on the request time, list `AddTask`, then `--search <flow id>` over the full range. Worked, but took one round trip per request, and a first attempt to bucket the executor runs by time with awk over the text list gave nonsense.
- What could have shown it: `marker info` on an `AddTask` marker naming the runnable that ran with that flow id, and when (or the reverse on the `Runnable`).

## (review) `--session` before the subcommand is rejected

- Command: `profiler-cli --session review-…-1 zoom push 25.10,25.31`
- Expected: the global option accepted anywhere, as the guide's `--session` examples suggest.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand's arguments.

## (review) Loading the raw Taskcluster URL selects a content thread

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/…/profile_browser_dbg-javascript-tracer-worker.js.json --session review-…-3`
- Expected: the parent-process GeckoMain selected, as when the same profile is loaded from its profiler link (`thread=0`).
- Got: `t-36 (GeckoMain, Privileged Content)` selected; my first marker searches returned nothing, with no hint that the thread was the reason.
- Workaround: `thread select t-0`.
