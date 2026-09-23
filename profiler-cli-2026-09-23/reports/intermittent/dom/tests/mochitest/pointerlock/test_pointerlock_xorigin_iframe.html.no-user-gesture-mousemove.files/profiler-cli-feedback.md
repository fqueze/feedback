## Log marker text shows as "(empty)"

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session nugm-1` (thread t-17, a mochitest per-test profile), and `profiler-cli marker info m-1030`.
- Expected: `INFO` rows showing the test's `info()` text, e.g. `file_pointerlock_xorigin_iframe_no_user_gesture.html: test sending mouse event to iframe`.
- Got: every `INFO` row reads `[(empty)] INFO: (empty)`; `marker info` shows `Level: (empty)`, `Message: (empty)`. `--json` has `value` set correctly and `formattedValue: "(empty)"`.
- Workaround: `marker info <m> --json` per INFO marker to read the `value`.

## Question: "which of these markers on different threads came first, when they are under 1 ms apart?"

- Command: `profiler-cli thread markers --list` and `profiler-cli marker info m-9226` (session nugm-1). Both print times at 1 ms resolution (`t=2.223s`, `Time: 2.223s`).
- Needed: the order of an APZ hit test (GPU process, 2223.03 ms) against the iframe's first `ViewManagerFlush` (2223.86 ms) and the popup's `mousemove` (2224.16 ms): all print as 2.223s/2.224s.
- Workaround: a python loop over `marker info <m> --json` to read `start` in ms.
- What would have answered it: a `--precise`/`--time-unit ms` option on `--list` and `marker info`, or printing more digits when neighbouring rows round to the same value; and a cross-thread list (`profile markers --search A,B --list` sorted by time, with exact times) to read the interleaving directly.

## Question: "which marker does this link's `marker=N` select?" (reviewer)

- Command: `profiler-cli load <profiler.firefox.com link with marker=12454>`, then `profiler-cli marker info m-6 --json` for each candidate handle, reading `markerIndex`.
- Needed: to check a report's link, the handle of the marker at index N on the link's thread. `marker info` without `--json` does not print the index, and nothing looks a marker up by its index.
- Workaround: a shell loop over `marker info <m> --json` for every handle in a `--search` list, printing `markerIndex`.
- What would have answered it: `profiler-cli load` printing the selected marker of a link it loads (handle, name, time), or `marker info --index N [--thread t-X]`; and `markerIndex` in the plain `marker info` output.
