## Question: how busy was the main thread in this range, counted in task time?

- Command: `profiler-cli zoom push 1.323,1.635` then `thread markers --search name:Runnable --list --limit 0 --json`, piped into a script that sums the union of the Runnable intervals (137 ms of 312 ms).
- Why: the startup profile's samples are too sparse to answer it (`thread samples --include-idle` gave 20 samples over 446 ms). I needed to know whether idle-priority tasks were blocked because the thread was busy, or despite it being mostly idle.
- What could show it: `thread markers` stats for interval markers could include "union coverage of the view", i.e. the busy fraction. Or add a `thread tasks` summary: time spent in tasks by priority, plus the longest gap with no task of a given priority (here there was no Idle-priority task from 1.322s to 1.635s).

## (review) `--search` does not match the text the list row prints for Task markers

- Command: `profiler-cli thread markers --session S --search "priority: Idle" --list --limit 0`, and `--search "Idle (0)"`.
- Expected: the idle-priority `Runnable` markers, since the list prints each row as `ChromeUtils::IdleDispatch - priority: Idle (0) task: ...`.
- Got: "No markers match the specified filters." Also `priority:Idle` matches nothing, because the `priority` field is the number 0.
- Workaround: `--search priorityName:Idle`, found by reading `marker info --json`. Suggestion: match the formatted row text too, or have "no match" list the searchable fields of the marker types in view.

## (review) Question: in what order was the idle queue filled, and which idle tasks were ahead of this one?

- Command: `thread markers --search priorityName:Idle --list --limit 0 --json` and `thread markers --search TaskController::AddTask --list --limit 0 --json`, joined by a script on the Runnable's `task` field and the AddTask's `flow` field.
- Why: to show from the profile, not only from the code, that the per-window tasks were queued ahead of BrowserGlue's second `idleDispatch`.
- What could show it: `marker info` on a Task marker could print when the task was queued (its AddTask) and its queue wait. A flow search could list a task's AddTask and Runnable together, as it already does for one id.

## (review) Question: how busy was the main thread in a range? (addendum)

- The union of `Runnable` markers understates it: microtask checkpoints run outside them (91 ms of 312 ms here), and so do macOS main-thread `HandleMainThreadCATransaction` paints with their sync IPC waits. The union of `Awake` markers overstates it on macOS: one `Awake` spanned 142 ms that included samples in `Native event loop idle`.
- What could show it: a busy/free summary per range that unions Runnable, microtask and CATransaction intervals, and lists the longest free stretches.
