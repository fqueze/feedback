# profiler-cli feedback — test_videocontrols_onclickplay.html

## `zoom push` does not take two marker handles

- Command: `profiler-cli zoom push m-14,m-2 --session …` (from one test's crash marker to the next test's marker, to see the browser restart between them)
- Expected: a zoom from the start of m-14 to the end of m-2, the way `ts-6,ts-12` works for timestamps.
- Got: `Error: Invalid time value: "m-14". Expected timestamp name (ts-X), seconds (2.7), milliseconds (2700ms), or percentage (10%)`
- Workaround: read both markers' times with `marker info --json` and zoomed by seconds.
