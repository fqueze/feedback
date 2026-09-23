## Question: in what order, to the millisecond, did markers happen late in a long profile?

- Command: `profiler-cli thread markers --category Test --search browser_shortcuts_add_search_engine --list --limit 0 --session <s>` on a 2m37s profile (task d9Emf5u1SCehJ_JU1H0VbA).
- Expected: timestamps precise enough to order markers a few ms apart, as it does before 60 s (`t=2.077s`).
- Got: past one minute every row prints `t=1m53s`, so the test's INFO lines, the `Link:SetIcon` message and the `TabAttrModified` event, 0.5 to 4 ms apart, all show the same time and their order cannot be read.
- Workaround: `--json` piped through a python script printing `start` in ms.
- What the output could show: `t=1m52.883s` (or seconds with ms, `t=112.883s`) instead of rounding to whole seconds.

## Question (review): which thread am I on after loading a raw Taskcluster profile URL?

- Command: `profiler-cli load 'https://firefox-ci-tc.services.mozilla.com/.../profile_browser_shortcuts_add_search_engine.js.json' --session S`, then `thread markers --search ... --list`.
- Expected: the parent process main thread selected by default, as when you load a profiler link with `thread=0`.
- Got: `t-14 (GeckoMain, WebExtensions)` was selected. The load output does not show the selected thread, and the query just printed "No markers match".
- Workaround: `thread select t-0`.
- What it could show: default to the parent process GeckoMain, or print the selected thread in the load output (it prints the view range and filters, but not the thread).
- Correction to the entry above: the load output probably does print `Selected thread`. I cut it off with `| tail -3`. What remains is the default of t-14 rather than the parent GeckoMain.
