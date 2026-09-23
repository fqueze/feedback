## `marker stack` on a marker listed as having a stack returns nothing

- Command: `profiler-cli marker stack m-122 --session browser-test_host_integration.js-1` (the same for m-123, m-144 and m-83, `C++ warning` / `C++ assertion` markers in an xpcshell resource-usage profile)
- Expected: the stack, since `thread markers --list` shows these markers with ✓ (has stack).
- Got: `[1] unknown!null`, and the same in `marker info`.
- Workaround: the assertion's stack is printed as separate `output` markers (`#01: …`). Warnings have none.
- Suggestion: don't mark a single null-frame stack as ✓, or say "stack present but empty".

## Question: in what order, and on which thread, were these log lines emitted?

- Command: `profiler-cli thread markers --session … --search test_host_integration --list --limit 0`
- Every replayed log line is shown as `t=4m47s`, which hides both the order and the gaps (66 ms between `exiting test` and the cache warning, 170 ms more to the assertion). The emitting thread (`StreamTrans #1` vs `Main Thread`) is only in `marker info`.
- Workaround: `--json`, then printing `start` (ms) and the `Thread` field from `fields` with a script.
- What the default output could show: millisecond times in `--list` when the range is short or many rows share a second, and the payload's thread name next to the label for `cppDebug` markers.
