## Question: in what order, to the millisecond, did these markers happen?

- Command: `profiler-cli thread markers --session <s> --search "browser-sidebar.js:511,sidebar-splitter,Idle (0),..." --list --limit 0`
- Expected: a start time precise enough to order markers within one test task (ms, as `profile-link.py --range` and the test's own races need): the DeferredTask run 4 ms after a `command` event, the idle gap of 114 ms.
- Got: every row in a 10 s test printed as `t=2m55s`; `marker info` likewise prints `Time: 5m16s - 5m16s (132.62μs)`. The list order alone cannot say whether a DeferredTask ran before or after a test assertion.
- Workaround: `--json` and a Python one-liner printing `start` with 3 decimals. A `t=175384.294ms` (or seconds with ms) column in `--list`, at least when zoomed to under a minute, would have answered it.

## Question (review): which idle-priority tasks ran in this window?

- Command: `profiler-cli thread markers --session S --search 'Idle (0)' --list --limit 0` (zoomed to a window where `--list` itself prints rows `Runnable ... IdleRequestExecutor - priority: Idle (0) task: ...`).
- Expected: those rows. The report under review used the same term (in its `markerSearch`) and concluded "no Idle (0) runnable runs" in a window where 10 did.
- Got: "No markers match the specified filters." `priority: Idle` also matches nothing; only `priorityName:Idle` or a bare `Idle` work, because the text is the formatted label, not a field value.
- Workaround: `--search priorityName:Idle`. Either match the row text `--list` prints, or say, on a search with zero matches, that label text is not searched and which field holds it.

## Question (review): when was this task queued?

- Command: `thread markers --search <task address> --list`, to pair a `Runnable ... task: X` with its `TaskController::AddTask (flow=X)`.
- Expected: the queue-to-run latency of one idle task, the key fact for an idle-starvation diagnosis.
- Got: the addresses are reused, so the search returns several unrelated AddTask/Runnable pairs, and the pairing (last AddTask before the run) has to be done by hand.
- Workaround: a Python pass over `--json`. A "queued at" / "waited" column on `Runnable` markers, from the matching flow marker, would have answered it.
