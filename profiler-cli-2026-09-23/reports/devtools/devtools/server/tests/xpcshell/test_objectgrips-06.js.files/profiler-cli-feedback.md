## Marker times past one minute lose sub-second precision

- Question: exactly when did this test's `test` marker start, relative to the first `Failed to launch ... (Error:-2147009284)` warning, 1-2 s earlier?
- Command: `profiler-cli thread markers --session test_objectgrips-06.js-2 --search test_objectgrips-06 --list --limit 0`, then `profiler-cli marker info m-1 m-3 m-7 --session test_objectgrips-06.js-2`
- Expected: start times such as `t=133.288s`, as it does for times under a minute (`t=46.650s`).
- Got: `t=2m13s` and `Time: 2m13s - 2m58s (45.029s)`, while the warning shows `t=2m12s`: the 1.6 s gap and its order cannot be read.
- Workaround: `marker info ... --json` and read `start`.
