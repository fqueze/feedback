## Question: "in what order, to the millisecond, did these markers happen" (profile longer than a few minutes)

- Command: `profiler-cli thread markers --session <s> --search 'smartwindow-group-tabs-panel,domwindowclosed,eventType:unload,nsXULPopupShownEvent' --list --limit 0` on a 7m59s profile.
- Expected: start times precise enough to order events ~50 ms apart (popupshowing, DOMWindowClose, unload, popupshown).
- Got: every row in that second printed as `t=5m` (and `t=1m1s` in another profile); only the row order hinted at the sequence. I had to run `marker info m-a m-b ... --json` and a Python one-liner to print `start`/`end` in ms.
- What would have answered it: in `--list`, print times with millisecond precision (e.g. `t=300.134s`) at least when zoomed, or a `--time-format ms` flag; `marker info` text output could also show the absolute start in ms.

## `zoom push` with an out-of-range value still pushes

- Command: `profiler-cli zoom push 300083,300140 --session <s>` (meant ms, forgot the suffix).
- Expected: refuse, since the range is outside the 478 s profile.
- Got: `Warning: Range extends outside the profile duration ... Did you mean to use milliseconds?` and the zoom was pushed anyway (`Zoom depth: 2`), so my next `zoom push` stacked on a bogus range until I noticed and ran `zoom clear`.
- Workaround: `zoom clear` then push again in seconds.

## Question: "when, and from where, was this runnable queued" (review, browser-review-browser_aiwindow_group_tabs_button_model.js)

- Command: `profiler-cli marker info m-10` on a `Runnable` marker (`nsXULPopupShownEvent - priority: Normal (4) task: 1da773d3f1b6d6ba0`), then `thread markers --search 1da773d3f1b6d6ba0 --list --limit 0`.
- Expected: `marker info` on a Runnable to name the `TaskController::AddTask` / `PutEvent` flow marker(s) that queued it, with their time and handle.
- Got: nothing linking them; I had to copy the task id into a full-profile `--search`. That search also returned unrelated later tasks reusing the same id (a `ProgressTracker::AsyncNotifyRunnable` a minute later), so I had to pick the nearest earlier AddTask myself.
- What would have answered it: in `marker info` for a Runnable/Task marker, a "Queued by" line with the nearest preceding flow marker of the same id (handle, time, the enclosing marker such as `RefreshDriverTick` / `Reflow`).

## `marker info --json` changes shape with the number of handles

- Command: `profiler-cli marker info m-303 --json` vs `profiler-cli marker info m-1..m-19 --json`.
- Expected: the same shape either way.
- Got: one handle returns the marker object itself; several return `{markers: [...]}`. My one-liner written for the second failed with `KeyError: 'markers'` on the first.
- Workaround: handle both shapes.
