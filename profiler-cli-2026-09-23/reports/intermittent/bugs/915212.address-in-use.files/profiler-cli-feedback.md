## Question: "how much network I/O did the job do in the 60 s before the server bind"

- Command: `profiler-cli zoom push 18,78.4 --session 915212.address-in-use-1` then `profiler-cli thread markers --search name:NetIO --json`
- Expected: sums or stats of the NetIO markers' numeric fields (Sent, Received, packets) over the zoomed range.
- Got: counts and duration stats only (`byType`), no field aggregates; the per-marker values need `--list` plus a script over 600 markers.
- Workaround: none; dropped the question (it would not have discriminated anyway).
- What would have answered it: a `--sum-fields` (or field stats in `byType`) for markers with numeric payloads.
