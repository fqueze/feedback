## Question: "the exact time of each line in the test's log"

- Command: `profiler-cli thread markers --category Test --search <test file> --list --limit 0`
- Expected: timestamps precise enough to order the test's steps against other markers (ms).
- Got: `t=1m15s` for every marker from 74.559s to 74.800s, so the order between steps and waits is lost. I needed `--json` and a script to print `start`.
- Could show: ms precision (e.g. `t=74.559s`) in `--list` output, at least when the view is longer than a minute.

## Per-test profile loads with a content-process thread selected

- Command: `profiler-cli load <per-test profile URL> --session ...`, then `thread markers --category Test --search <test> --list`
- Expected: the parent process main thread selected by default (as with other profiles), since that is where the `Test` markers are.
- Got: `t-22 (GeckoMain, Privileged Content)` selected, and "0 markers (filtered from 17238)" with no hint that the markers are on another thread.
- Workaround: `profile info --search GeckoMain`, then `thread select t-0`.

## Question: "which marker index does this handle have" (reviewer, checking a link's `marker=N`)

- Command: `profiler-cli marker info m-401 --session ...`
- Expected: the marker's index in its thread's marker table (what a profiler.firefox.com link's `marker=N` is), next to its time.
- Got: name, type, time and fields, no index. I needed `marker info m-a m-b ... --json` and a script to print `markerIndex` and `start` for each quoted marker.
- Could show: `Index: 9822` in the text output of `marker info`, and accept `--marker-index N` in `thread markers` to find the handle from a link.
