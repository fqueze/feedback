# profiler-cli feedback

## Marker times rounded to whole seconds in a 5-minute profile
- Command: `profiler-cli thread markers --session <s> --category Test --search browser_ext_themes_validation.js --list --limit 0`, and `profiler-cli marker info m-6 m-31 m-16`
- Question: when did the test start, end, and fail, to the millisecond (the whole test ran in 10 ms, the vsync wait 8.7 s)?
- Expected: times with ms precision (e.g. `t=293.020s`), at least in `marker info`, which is the detail view.
- Got: `t=4m53s` for both "Entering test" and "Leaving test"; `Time: 4m53s (instant)` in `marker info`.
- Workaround: `marker info ... --json` and read `.start`.
