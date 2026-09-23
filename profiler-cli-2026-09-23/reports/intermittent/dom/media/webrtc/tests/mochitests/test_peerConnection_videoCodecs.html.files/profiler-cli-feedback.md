## `zoom push` does not take two marker handles as a range

- Command: `profiler-cli zoom push m-12610,m-15127 --session ...`
- Expected: zoom from the start of m-12610 to the end of m-15127 (two instant log markers bounding one loop iteration).
- Got: `Error: Invalid time value: "m-12610". Expected timestamp name (ts-X), seconds ...`
- Workaround: `marker info` on both to read their times, then `zoom push <s>,<s>` — but `marker info` prints times rounded to the second (`20m4s`), so I had to go through `--json` to get usable bounds.

## Machine CPU over a range (resource-usage profile)

- Question: "what was the machine's CPU use during this 5.6 s window, compared with before it" (to tell a waiting stall from a saturated machine).
- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json | python3 ...` to average `cpuPercent` over a window.
- Could show: `thread markers --search "name:CPU Use"` in a zoom could summarise the numeric payload fields (min/mean/max of cpuPercent) the way `counter info` does for counters.

## Test log values embedded in INFO markers

- Question: "for each loop iteration of the test, how long did the media-flow wait take and what did the first stats report say" (values live inside JSON in `Got stats: {...}` INFO marker labels).
- Command: `thread markers --search test_peerConnection_videoCodecs --list --limit 0 --json` then a Python script parsing the labels.
- Could show: nothing reasonable in the tool itself; noting it as the one place the default output could not answer. `--list` labels are cut at the terminal width even with COLUMNS=300, so a single long label could not be read without `--json` or `marker info`.
