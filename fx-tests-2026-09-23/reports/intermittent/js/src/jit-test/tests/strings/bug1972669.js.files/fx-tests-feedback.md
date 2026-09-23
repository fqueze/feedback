## Question: "is this jit-test still failing, since when, and on which worker image?"

- Command: `fx-tests test js/src/jit-test/tests/strings/bug1972669.js` (also `--history`)
- Expected: pass/fail rates and history for a jit-test, or failing that, a message saying jit-test isn't indexed.
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...`, followed by "renamed / added after the window / never run in CI". None of those was true: the jit-test harness just isn't covered, and the message doesn't say so.
- `fx-tests intermittent --bug 2055448` (default 7 days) gave "no sheriff annotations". That was actually the key signal that the test had stopped failing, but I only saw it by widening with `--since 21 --tree all`. When the wider window has annotations, the message could say when the last one was.
- Workaround: pulled every `test-windows11-64-25h2-ccov/opt-jittest-1proc-6` job from the Treeherder jobs API, downloaded 70+ live_backing.log files, and scripted per-variant pass/fail, durations, and the `sboms/win11-64-25h2-X.md` worker image out of each one. That script is what showed the step at the worker-image rollover (1.0.7 -> 1.0.8). What would have answered it: jit-test support in `fx-tests test --history`, with the worker image as a column.
- `fx-tests task <jittest task>` fails with "no profile_resource-usage.json" and prints nothing else. It could still list the job's test outcomes from the log.
