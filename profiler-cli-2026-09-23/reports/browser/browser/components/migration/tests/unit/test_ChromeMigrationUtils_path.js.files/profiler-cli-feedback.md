## Question: did two xpcshell tests overlap in time, in a resource-usage profile?

- Command: `profiler-cli marker info m-24 m-25 m-26 m-2 --session <s>` on a 21m59s resource-usage profile (task S_bY3Qk0RVuY0GWAMORJ_A).
- Expected: start/end times precise enough to tell whether two `test` markers overlap (they are ~1 s long and start ~55 ms apart).
- Got: `Time: 3m59s - 4m (1.193s)` for every marker; the `--list` view also prints `t=3m59s` for all of them. Overlap and ordering could not be read.
- Workaround: `--json | jq '.markers[] | .start, .end'`. The default output could print milliseconds (or seconds with 3 decimals) when the marker is shorter than a few seconds, or always in `marker info`.

## Question: which samples fall inside a 135 ms task, in a 581 ms per-test profile? (review)

- Command: `profiler-cli zoom push 383,519 --session <s>` then `thread samples-top-down --search getDataPath` on the per-test profile of task S_bY3Qk0RVuY0GWAMORJ_A (581.71 ms long).
- Expected: either a zoom to 383–519 ms, or an error that 383 s to 519 s lies outside a 0.58 s profile.
- Got: the zoom was accepted as seconds (`View: ... (2m16s)`), and the samples command answered `No samples matched --search "getDataPath"`, which reads as "the function never ran" rather than "your range is empty".
- Workaround: `zoom push m-3` (the task marker). `zoom push` could reject or warn on a range that does not intersect the profile, or accept `383ms,519ms`.
