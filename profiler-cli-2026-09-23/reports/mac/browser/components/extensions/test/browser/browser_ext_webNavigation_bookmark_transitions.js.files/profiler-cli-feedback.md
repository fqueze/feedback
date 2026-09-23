## When did this test's markers happen, and in what order? (list times past 60 s)
- Command: `profiler-cli thread markers --session <s> --category Test --search webNavigation_bookmark_transitions --list --limit 0`
- Expected: row times with sub-second resolution, as below 60 s (`t=331.651s` or `5m31.651s`).
- Got: all 60 rows of the test read `t=5m32s`, then `t=5m41s` for the failures; `marker info` prints `Time: 5m32s` too. The 8.6 s vsync wait and ms-apart subtests cannot be placed or ordered from the text.
- Workaround: `marker info <handles> --json` / `--list --json` and read `start` (ms).
- Already reported by several sibling reports in this directory; logged again as it cost a script here too.

## Which threads ticked a refresh driver during this wait, and when?
- Command: `profiler-cli profile markers --search RefreshDriverTick --limit 0` (zoomed on the wait)
- Got: the per-thread counts are there ("Matches by thread:", at the end), but the rows show `t=5m32s` for every row, so whether the parent's 4 ticks were at the start of the 8.6 s wait or spread over it needed `--json` and a script.
- What would have shown it: sub-second row times, as in the first entry.

## (review) Which marker does this profiler link's `marker=N` point to?
- Command: `profiler-cli load '<profiler.firefox.com link with marker=1203078&thread=0>' --session s`, then `profiler-cli marker info --session s m-72`
- Expected: `load` to say which handle the link's marker is (e.g. `Link marker: m-72`), or plain `marker info` to show the marker index and a sub-second time.
- Got: `load` shows only the selected thread. Plain `marker info` shows `Time: 4m44s` and no index. Checking 24 links meant running `marker info … --json` through a script for `markerIndex` and `start`.
- What would have shown it: the link's marker handle printed on `load`, plus `Index:` and ms-resolution `Time:` in the plain `marker info` output.
