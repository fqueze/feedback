## GC marker payloads are truncated, even with --json

- Question: at what time did this incremental GC mark its roots and sweep (i.e. which slice moved MarkRoots->Mark and Mark->Sweep)?
- Command: `profiler-cli marker info m-427 --json --session <s>` (a GCMajor), and the same on GCSlice markers.
- Expected: the full `timings` payload (phase times, initial_state/final_state per slice).
- Got: `"value": "{\"status\":\"completed\",...,\"minor_gc_number\":22,\"", "truncated": true` -- the JSON string is cut, so it cannot be parsed; the text output is cut too.
- Workaround: one `marker info` per GCSlice, regex `"initial_state"`/`"final_state"` out of the text before the cut. A per-slice state column in `thread markers --search name:GCSlice --list` would have answered it in one call.

## screenshots --at does not say when or for which window each image was captured

- Question: which window does each screenshot show, and was it still current at time t?
- Command: `profiler-cli screenshots --at 48.4 -o dir --session <s>` and `--at 6.7`.
- Expected: per image, the window ID and the capture time (the marker start), so a frame last updated 42 s earlier is obvious.
- Got: `Wrote 3 image(s): screenshot-m-86.jpg ...` -- identical files for both times, no time or window.
- Workaround: `marker info m-N --json | jq '.start, (.rawFields[]|select(.key=="windowID"))'` per marker, then list CompositorScreenshot markers in a zoom to see the last frame of each window.
