## Question: in what order, to the millisecond, did the test's steps happen?

- Command: `profiler-cli thread markers --category Test --search browser_inspector_textbox-menu.js --list --limit 0 --session <s>` on a 2m36s profile.
- Expected: timestamps precise enough to order and space markers (ms).
- Got: every row printed as `t=2m35s` or `t=2m36s`, so the whole test (about 1 s) collapses to two values; the same in `marker info` (`Time: 2m36s`).
- Workaround: re-ran with `--json` and read `start` from `flatMarkers` / `marker info --json`.
- What could have shown it: millisecond precision in the `t=` column (e.g. `t=155.793s` or `2m35.793s`), at least in `--list` mode.

## Question (review-browser_inspector_textbox-menu.js): which marker index does this handle have, to check a link's `marker=N`?

- Command: `profiler-cli marker info m-19 --session review-textbox-menu-2`
- Expected: the marker's index in the thread (the `marker=N` a profiler link uses), next to Time/Thread.
- Got: Type, Category, Time, Thread, Fields; no index.
- Workaround: `marker info m-19 m-114 --json` piped through a python one-liner to print `markerIndex`.
- What could have shown it: an `Index: 66237` line in the default output.
