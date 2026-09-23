## Question: "in what order, to the millisecond, did these markers happen?"

- Command: `profiler-cli thread markers --category Test --search restored-multiple-grids --list --limit 0 --session <s>` (and the same with `--search RDP`, and `marker info m-37 m-42 m-44`), on a 2m24s profile.
- Expected: start times precise enough to order markers that are tens of ms apart (e.g. `t=2m21.887s`, or ms relative to the zoom).
- Got: every row printed `t=2m22s`, and `marker info` printed `Time: 2m22s`; about 40 markers (reload start, target-destroyed, the rejection, target-switch) are indistinguishable in time.
- Workaround: `--json` and read `flatMarkers[].start` with a Python one-liner. The text output could show ms precision (at least when the view range is under a few seconds, or always for `--list`).
- Also: `--list --json` gives `duration` but no `end`; fine, but the text list's duration column is the only human-readable one.
