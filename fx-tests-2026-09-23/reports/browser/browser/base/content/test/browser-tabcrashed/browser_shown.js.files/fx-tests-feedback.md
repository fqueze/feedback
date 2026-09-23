## "9 failing executions of 9" for a single timeout
- Command: `fx-tests task fx54zwpgQpCPPLSGbcmy9w --profiles --messages`
- Expected: one TIMEOUT execution of browser_shown.js (standalone job, the test ran once).
- Got: `CRASH, TIMEOUT — 9 failing executions of 9`, where the 9 are the minidumps the harness wrote when it force-killed the hung browser's 9 processes (`processing 9 crashes`).
- Workaround: read the resource-usage profile to see that the test ran once.

## A moved test needs one query per path
- Question: the failure history of a test that bug 2069131 moved (`tabcrashed/` to `browser-tabcrashed/`).
- Command: `fx-tests test <new path>` and `fx-tests test <old path>`, then adding up the counts by hand.
- Expected: some hint that the history begins on 2026-09-11 because the file was renamed, or a way to merge both paths.
- Got: two separate reports. Their `--issue` numbers also differ (issue 1 is the timeout under the new path and the leak under the old one).
