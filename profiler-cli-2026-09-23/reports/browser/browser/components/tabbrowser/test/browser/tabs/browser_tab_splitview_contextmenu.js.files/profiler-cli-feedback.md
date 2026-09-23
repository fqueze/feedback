## Question: in what order, to the millisecond, did the test's log lines and DOM events happen?

- Command: `profiler-cli thread markers --category Test --search browser_tab_splitview_contextmenu --list --limit 0 --session <id>` (and the same with `--search tab-preview-panel` after `zoom push 325.8,334.5`)
- Expected: start times precise enough to order events within a test that lasts 3 s (e.g. `t=328.9445s`, or ms relative to the zoom start).
- Got: every row printed as `t=5m26s` / `t=5m29s` (whole seconds), so ~120 test-log rows and ~130 panel events all shared 3-4 distinct timestamps. The order between the cleanup function's `tabstrip-orientation-change`, the harness's `mousecancel` and the refresh tick was the whole diagnosis and was invisible.
- Workaround: `--json` piped through a python one-liner printing `start/1000` with 4 decimals, for every list I read.
- What the output could have shown: time with millisecond precision once the profile is longer than a minute (or when zoomed to a range under a few seconds).

## Question (review): which window's refresh driver ran this tick?

- Command: `profiler-cli thread markers --search RefreshDriverTick,Synthetic --list --limit 0 --session <id>` on a parent main thread with two chrome windows.
- Expected: something in each row that tells the main window's ticks apart from another window's, since only the main window's tick services its `Synthetic mouse move event` observer.
- Got: identical-looking `RefreshDriverTick` rows. The distinguishing `innerWindowID` only shows up in `marker info` ("Other payload fields") or in `--json` `rawFields`. A catch-up tick for another window looked at first like the main window ticking without servicing its pending observer.
- Workaround: `marker info <handles…> --json | jq` on `rawFields[] | select(.key=="innerWindowID")` for each tick.
- What the output could have shown: the `innerWindowID` (or window/document URL) as a column in `--list`, or accepted by `--search innerWindowID:4`.

## `marker info --json` returns a different shape for one handle and for several

- Command: `profiler-cli marker info m-64 --json` vs `profiler-cli marker info m-61 m-280 --json`
- Expected: the same shape either way.
- Got: one handle gives the marker object at the top level; several give `{ markers: [...] }`. A jq filter written for one broke on the other.
- Workaround: `jq '.markerIndex // .markers[0].markerIndex'`.

## `--session` rejected before the subcommand

- Command: `profiler-cli --session <id> zoom push 328.70,328.98`
- Expected: a global option accepted in any position.
- Got: `error: unknown option '--session'`.
- Workaround: put `--session` after the subcommand.
