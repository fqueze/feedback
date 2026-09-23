## Marker times past one minute lose their sub-second part (browser_downloads_panel_disable_items.js)

- Question: at what exact time did the TEST-UNEXPECTED-FAIL happen, so I can pull the screenshot just before it?
- Command: `profiler-cli thread markers --session browser-disable_items-1 --thread t-0 --category Test --search browser_downloads_panel_disable_items --list --limit 0`, then `profiler-cli marker info m-16 --session browser-disable_items-1`
- Expected: a time I can pass to `screenshots --at` (e.g. `t=140.630s`).
- Got: `t=2m21s` in the list and `Time: 2m21s` in `marker info`; every marker of the test showed `2m20s`/`2m21s`, so their order within the second and the gap to the screenshots were unreadable.
- Workaround: `marker info m-16 --json | python3 -c ...['start']` for each marker. Showing seconds with milliseconds (`140.630s`, or `2m20.630s`) would have answered it.
