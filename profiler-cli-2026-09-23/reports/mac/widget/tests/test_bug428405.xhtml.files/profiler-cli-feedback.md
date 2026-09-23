## Question: which document did each style flush restyle?

- Command: `profiler-cli thread markers --session S --search "name:Styles" --list --limit 0`
- Needed: for each `Styles` marker, the document it restyled, to show that the test document was not
  restyled between a tab switch and a focus() call.
- Got: the list rows show only name, time and duration; `innerWindowID` (and the elements-styled count)
  is only in `marker info` or `--json`. Scripted over `--json` to print `data.innerWindowID`.
- Could show: the inner window ID (or the document URL it maps to) on `Styles`, `DoFlushPendingNotifications`,
  `SetNeedStyleFlush` rows in the flat list.

## Question: did event X happen inside marker Y's interval?

- Command: `profiler-cli thread markers --session S --list` around a 10 ms window
- Needed: whether a `focus` DOMEvent fell inside a `load` handler, and whether a `FocusBlurEvent` fell
  inside a `RefreshDriverTick`.
- Got: `t=7.023s` for every row in the same millisecond, so ordering and containment within the ms are
  not readable; list order is by start only. Scripted over `marker info ... --json` for `start`/`end`.
- Could show: sub-millisecond start (and end) times once the view is zoomed below ~50 ms, or nesting.

## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session test_bug428405.xhtml-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a clear hint to set
  `PROFILER_CLI_SESSION_DIR`. One wasted load; the hint was good.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`.

## Question: when was this task queued, and what was queued ahead of it? (review-test_bug428405.xhtml)

- Command: `profiler-cli thread markers --session S --search "name:Runnable,name:TaskController::AddTask" --list --limit 0`
  over a 1.5 ms zoom
- Needed: for each `Runnable`, the time its task was queued (its `TaskController::AddTask` with the same
  flow id), to tell in which interval a runnable dispatched from an unprofiled thread (the vsync
  notification) must have been queued, from the queue order of the tasks run around it.
- Got: `Runnable` rows show `task: <flow>` in the label and `AddTask` rows `flow=<flow>`, but nothing joins
  them; flow ids are pointers and get reused within a few ms. Scripted over `--json`, matching each
  runnable to the latest earlier `AddTask` with its flow id.
- Could show: on `Runnable` rows (or in `marker info`), the queue time and queue-to-run delay from the
  matching `AddTask`, and a "queued before/after" view around a selected runnable.
