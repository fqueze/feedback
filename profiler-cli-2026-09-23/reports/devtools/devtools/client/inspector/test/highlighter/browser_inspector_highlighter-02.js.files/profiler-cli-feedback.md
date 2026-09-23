## Question: which of two markers less than 1 ms apart came first, and by how much

- Command: `profiler-cli thread markers --search "getMutations,anonymousroot" --list --limit 0`
- The list rounds start times to 1 ms (`t=8.652s`, `t=8.654s`), and it sorts by start time, so ordering is visible. The gap between the end of one marker and the start of the next is not: here, `getMutations()` ended 55 µs before `anonymousrootcreated` was dispatched. That gap is the whole race.
- Workaround: `profiler-cli marker info m-363 m-245 --json | python3 ...` to read `start`/`end` in µs.
- What would have answered it: µs precision (or `end`) in `--list` when the view is zoomed below ~50 ms, or a `marker info` text output that prints start and end with µs precision.
