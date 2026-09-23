## Question: "in which order, to the millisecond, did these markers from different tests happen?" (resource-usage profile)

- Command: `profiler-cli thread markers --session <s> --search test_distribution --list --limit 0` on a 25-minute xpcshell resource-usage profile.
- Expected: start times precise enough to order markers within one second (for example `t=406.172s`), and for interval markers, their end.
- Got: every row read `t=6m46s`, so the six concurrent tests' intervals and the failure's position among them could not be read. That was the whole question.
- Workaround: `--json` and a Python one-liner printing `start/1000` and `start+duration`.

## Review: which test's log line came first, to the millisecond, in a long resource-usage profile (browser-review)
- Command: `profiler-cli thread markers --session <s> --search test_distribution --list --limit 0` on a 25-minute resource-usage profile (HkQ2vmR1SaSpZam3r1sLEg).
- Expected: start times precise enough to order markers a few ms apart (406.148 vs 406.172 s), and end times for interval markers.
- Got: every row shows `t=6m46s`, and the output had no end time for the `test` interval markers, so I could not tell whether a sibling had exited before the failure.
- Workaround: `--json`, then a Python script over `flatMarkers[].start/duration`. It would help if `--list` printed seconds with ms (`406.172s`) and each interval's end, or had a `--precise` flag.
