## Question: which failure modes are behind a catch-all bug (here "shutdown hang")?

- Command: `fx-tests intermittent --bug 2060167 --limit 0` (and `--json`)
- Expected: occurrences grouped by the lines that tell the modes apart. For a shutdown hang those are the watchdog's `RunWatchdog: Mainthread nested event loops during hang: --- <loop>` and `RunWatchdog: Shutdown hanging at step <phase>`, plus the `PROCESS-CRASH ... | <test>` line naming the last test.
- Got: `lines` per occurrence holds only the `TEST-UNEXPECTED-FAIL | shutdown hang | profile uploaded in ...` line, which is identical for all 63 jobs. "Failure messages, per annotated job" lists only the 8 `leaked window` lines.
- Workaround: I downloaded all 63 `live_backing.log` files, then used a script to pull out the step, the nested loop and the crash test. That gave 5 modes (21 / 19 / 13 / 7 / 3).
- It could show: for a bug whose lines are all the same, the next 2-3 log lines after the matched one (the harness prints the discriminator right after it), grouped by count.

## Question: which task runs passed this test on this config (a clean baseline log)?

- Command: `fx-tests test netwerk/cookie/test/browser/browser_sharedWorker.js --task-ids`
- Expected: some passing task IDs to diff a log against.
- Got: only failing tasks. The hang is not attributed to the test, so nothing was listed.
- Workaround: `treeherder-cli --repo autoland --similar-history 594551238 --json`, then the Treeherder jobs API to map job IDs to task IDs.
