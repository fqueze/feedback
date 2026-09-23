# profiler-cli feedback — browser_892956_destroyWidget_defaultPlacements.js

## Times past one minute are printed to the second

- Command: `profiler-cli thread markers --session b892956-1 --category Test --search browser_892956 --list --limit 0`
- Question: "when did this test enter, leave, and fail?"
- Expected: `t=65.478s`, as for markers before 60 s (`t=9.597s`).
- Got: `t=1m5s` for every marker of the test, and `t=1m12s` for the failure, so the 6.9 s wait and the order of markers 1 ms apart are unreadable. `marker info` prints `Time: 1m5s` too.
- Workaround: `--json` and read `start`.

## "What started in this window" is buried under long markers that merely overlap it

- Command: `profiler-cli zoom push 10.318,10.340` then `profiler-cli thread markers --search "-name:Runnable,-name:Preference Read" --list --limit 200`
- Question: "what happened on the main thread in the 20 ms around this `dragstart`?"
- Expected: the markers that start in the zoomed range, in order.
- Got: 80+ rows of `IPCOut`/`IPCIn` markers that started at t=1.04s and last 11–40 s, before anything that happened in the window. Excluding them by name works for IPC, but every long interval marker (`test`, `task`, CSS animations) has the same effect.
- Workaround: `--json`, then a script keeping `start` within the range (`inrange.py` in this directory). An option such as `--starts-in-view` would answer it.

## `--list --json` drops the payload fields

- Command: `profiler-cli thread markers --session b892956-1 --category Test --search "Entering test" --list --json`
- Question: "which test is each `Entering test` marker from?"
- Expected: the `Test Name` field that `marker info` shows.
- Got: `handle`, `label`, `start`, `hasStack` only; `label` is `Entering test ` for every test's first task.
- Workaround: one `--search <test file name>` call per test, or the `test` markers (`--search name:test`), which carry the path in their label.
