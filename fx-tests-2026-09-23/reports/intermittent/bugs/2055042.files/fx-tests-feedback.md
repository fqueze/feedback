# fx-tests feedback (bug 2055042)

## Question: "how often does this crashtest fail, per config, and in how many runs?"
- Command: `fx-tests test dom/media/test/crashtests/1859384.mp4` (also tried the real culprit 1850453.html)
- Expected: rates per config, the way it works for mochitest/xpcshell.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." — the reftest/crashtest harness is not indexed at all, so there is no run denominator, no `--history`, no `--task-ids` for crashtests.
- Workaround: Treeherder `api/failuresbybug` + `api/failurecount` (failures vs pushes, not jobs).

## Question: "when did this bug start failing, on each platform?"
- Command: `fx-tests intermittent --bug 2055042 --limit 0`
- Expected: a way to see the bug's annotations back to when it was filed (2026-07-14), per day and per platform, to date the regression.
- Got: only the last 7 days (62 of 466 annotations). The step change (macOS-only from 2026-07-14, Windows/Linux from 2026-08-26) was invisible.
- Workaround: `curl https://treeherder.mozilla.org/api/failuresbybug/?bug=2055042&startday=2026-07-01&endday=...&tree=all` and a Python per-day/per-OS count. A `--history` (per-day, per-platform sparkline) on `intermittent --bug` would have answered it.

## Question: "which test was running when the assertion fired?"
- Command: `fx-tests intermittent --bug 2055042` then `fx-tests task <id> --profiles`
- Got: intermittent says "Tests named, per annotated job: (none ...)"; `task` warns "CRASH dom/media/test/crashtests/1859384.mp4" (the crash is attributed to the *next* test, which is misleading: the assertion comes from the previous test's decoder, 7-211 ms after `TEST-END | 1850453.html`).
- Workaround: downloaded all 62 live_backing.log files and scripted "last TEST-START / TEST-END before the assertion line". Showing the preceding TEST-END (and its delay) next to a CRASH attributed to a test that had just started would have answered it.
