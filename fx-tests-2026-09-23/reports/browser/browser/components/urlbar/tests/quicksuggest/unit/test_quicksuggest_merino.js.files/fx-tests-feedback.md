## Question: "how long did this test take in each test-verify step (normal vs chaos), on this job and on another platform's job?"

- Command: `fx-tests task <TV taskId> --json --passed` to find whether the test ran in a chunk, then `profiler-cli` over the resource-usage profile and a script bucketing `test` markers by the preceding `::: Running test verification step` INFO marker.
- Expected: `fx-tests task` on a test-verify job to show, per test, its executions split by verify step with their durations (e.g. "normal 10x 8.2 s, chaos 1x 120.5 s TIMEOUT"). That comparison (Linux chaos 24.7 s vs macOS chaos >120 s on the same push) was the key evidence.
- Got: executions counted, no per-step breakdown, no durations.

## `fx-tests test <path> --bugs` printed no bug

- Command: `COLUMNS=250 fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_merino.js --bugs --full-messages`
- Expected: bug 2003715, whose summary names the test and which has 2 sheriff annotations inside the window (2026-09-02, test-verify jobs).
- Got: the normal output with no bugs section and no "none found" line either, so I could not tell "no bugs" from "section not printed". Possibly because the annotated jobs are test-verify, outside the xpcshell data; if so, saying so would help.
