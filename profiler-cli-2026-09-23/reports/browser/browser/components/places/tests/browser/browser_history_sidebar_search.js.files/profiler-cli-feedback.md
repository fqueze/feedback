## Question: "the times of these markers, to the millisecond"

- Command: `profiler-cli thread markers --session <s> --category Test --search <test file> --list --limit 0`, on a 6m48s profile.
- Expected: the start of each row, and its duration for intervals, to about 1 ms.
- Got: `t=6m42s`, `t=6m48s`, rounded to the second. The BUSY `Counter::add` (139.577 s) and the `TEST-UNEXPECTED-FAIL` it causes (139.578 s) look simultaneous with everything else in that second. The gap between insertMany's end and the start of waitForCondition cannot be read either.
- Workaround: `--json`, then a Python one-liner printing `start/1000` and `start+duration`. I needed it on every marker list in this investigation.
- What would help: ms precision, or a precision that scales with the zoom (`--time-precision`?). At least when the profile is longer than a minute.
