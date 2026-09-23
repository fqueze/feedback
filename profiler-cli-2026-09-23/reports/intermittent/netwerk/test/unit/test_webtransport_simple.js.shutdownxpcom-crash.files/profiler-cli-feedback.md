## Question: "at what time, to the millisecond, did each line of this test's log happen" in a resource-usage profile

- Command: `profiler-cli thread markers --search test_webtransport_simple --list --limit 0` on a 25-minute resource-usage profile.
- Expected: timestamps precise enough to order the shutdown sequence (exit at 724.170 s, socket-thread warnings at 724.252 s, main-thread warning at 724.253 s, crash at 724.614 s).
- Got: `t=12m4s` on every row: the whole sequence collapses into one second. I had to go through `--json` and a script over `flatMarkers[].start`.
- Also: a single `deepEqual` PASS message of 1,199 array elements printed in full (about 25 KB on one row), which pushed the list past the tool-output limit. A per-row truncation (with `marker info` for the full text) would have kept the list readable.

## `✓ = has stack trace` on markers whose stack is empty

- Command: `profiler-cli marker info m-142 --session …` (a `cppDebug` marker from the xpcshell harness in a resource-usage profile).
- Expected: either a native stack for the warning, or no ✓ in the list.
- Got: the list marks it ✓, and `marker info` prints `Stack trace: … [1] unknown!null`. Every C++ warning in these profiles is like that, so the ✓ sends you to look at a stack that is not there.

