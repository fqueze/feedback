## Marker times past one minute lose their milliseconds

- Command: `profiler-cli thread markers --category Test --search browser_resize_sidebar --list --limit 0 --session browser_resize_sidebar.js-2` and `profiler-cli marker info m-87 m-106 m-107 m-11 --session browser_resize_sidebar.js-2` (profile P_jZ65GEQJi3Y-BdBsvA1Q, test at 1m20s..1m28s).
- Expected: `t=83.150s`, as for markers under a minute (`t=28.596s`).
- Got: `t=1m23s` for every marker in that second, both in the list and in `marker info` (`Time: 1m23s`), so the order and spacing of 50 polls, and the time to pass to `screenshots --at` or `zoom push`, cannot be read.
- Workaround: `marker info ... --json` and read `start` (ms).

## Question: which markers in a range were caused by a given JS function (review-browser_resize_sidebar.js)

- Question: "which `SetNeedStyleFlush` markers between 28.585 s and 37.03 s have `toggleExpandOnHover` in their stack", i.e. did anything toggle the root attribute again after the stale call re-added it.
- Command: `profiler-cli zoom push 28.609,37.03`, `profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0` (154 rows, all with a stack but no hint of it), then `profiler-cli marker info <all 154 handles> --json` and a Python filter over `stack.frames[].name`.
- What the output could have shown: a way to filter markers by a frame in their captured stack (e.g. `--search stack:toggleExpandOnHover`), or the innermost JS frame of the stack in the `--list` row, so the default list answers "who dirtied style here" without a script.
