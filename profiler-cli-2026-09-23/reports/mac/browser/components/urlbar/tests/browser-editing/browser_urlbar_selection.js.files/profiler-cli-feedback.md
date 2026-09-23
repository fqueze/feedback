## `zoom push` does not take a pair of marker handles

- Command: `profiler-cli zoom push m-18,m-16 --session browser_urlbar_selection.js-1`
- Expected: zoom from the start of m-18 (the `Entering test` INFO marker) to m-16 (the timeout failure).
- Got: `Error: Invalid time value: "m-18". Expected timestamp name (ts-X), seconds (2.7), milliseconds (2700ms), or percentage (10%)`
- Workaround: read both markers' `start` from `thread markers --json` and zoom with seconds. Instant markers
  are the natural bounds of "from this test step to the failure", so `m-A,m-B` would save a JSON round trip.
