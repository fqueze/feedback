## Question: how late did each setTimeout fire, and which ones fired together?

- Command: `profiler-cli thread markers --session ctc-1 --search "setTimeout callback" --list --limit 0`
- Expected: for each `setTimeout callback` marker, when the timeout was set (the stack's "Captured at"), so that lateness = start - set time - interval can be read, and a 0 ms timeout that waited 22 ms behind an overdue 1000 ms timeout of the same window stands out.
- Got: the list shows the start, duration and label only; the set time is only in `marker info` ("Captured at"), one marker at a time. `innerWindowID` is also only in `marker info` (as an unlabelled raw field).
- Workaround: a script (`timer_lateness.py` next to this file) running `thread markers --json` then `marker info --json` in batches of 40, computing `start - stack.capturedAt - interval` per marker. Default output could show "set at t=… (+N ms late)" and the window id on each `setTimeout callback` row.

## review-browser_markup_events_click_to_close.js (2026-09-22)

- `review-brief.md` says to open a report's link with `profiler-cli load <profiler.firefox.com link>`. That works, but `profile-link.py --session <id> --marker m-N` then refuses with "A link needs the raw artifact URL: load the Taskcluster URL itself". Expected: it builds links from a session loaded from a `from-url` link, since the raw URL is inside it. Got: an error. Workaround: stop the session and reload the raw Taskcluster URL, which costs another 0.6 GB load. Question behind it: "link a new observation during a review".
- `profiler-cli load` failed with EPERM on `~/.profiler-cli` in the sandbox. The error message suggests `PROFILER_CLI_SESSION_DIR`, which worked, but the briefs never mention it: every call then needs both `PROFILER_CLI_SESSION_DIR` and `PROFILER_CLI_SESSION_OWNER` in front.
