## `fx-tests test <path> --bugs` prints nothing about bugs

- Command: `fx-tests test devtools/shared/transport/tests/xpcshell/test_packet.js --bugs`
- Expected: a bugs section, even if only "no bug names this test". Better still, the bug(s) the failing jobs were starred on. Here that is bug 1991833 (XfKT04wZR42kSNwV6wuRpQ.0 is among its annotations).
- Got: exactly the same output as without `--bugs`, with no bugs line at all. I could not tell "no bug" from "flag ignored".
- Workaround: a Bugzilla quicksearch for "msix Access is denied", then `fx-tests intermittent --bug 1991833 --since 21 --limit 0 | rg <taskId>` to confirm the star.

## `fx-tests task` does not say that the job broke down as a whole

- Command: `fx-tests task XfKT04wZR42kSNwV6wuRpQ.0 --profiles`
- Question: "did this job break down as a whole, and when?" The job has 1,375 TIMEOUTs out of 1,854 tests. 1,461 of them had no process at all (the harness logged `not killing -- proc or pid unknown`), and the job ends on a single `PermissionError: [WinError 5] Access is denied` traceback.
- Got: per-test rows, "Test timed out" each, alphabetically, with nothing about the onset time or the traceback.
- What would have answered it: one header line such as "1,375 TIMEOUTs began within 6 s from t=54.1 s; job traceback: PermissionError ... CreateProcess". I had to load the resource-usage profile and script over its `--json` to see that.
