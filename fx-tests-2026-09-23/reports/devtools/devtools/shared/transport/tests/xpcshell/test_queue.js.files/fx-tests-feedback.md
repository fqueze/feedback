## Job-wide failure presented as a per-test intermittent (test_queue.js)

- Command: `fx-tests test devtools/shared/transport/tests/xpcshell/test_queue.js` (and `--task-ids`)
- Expected: some sign that the one failing task was a job-wide collapse. In XfKT04wZR42kSNwV6wuRpQ, 1,375 of 1,854 tests timed out, so this test's TIMEOUT is not its own. Something like "failing job had 1376 failing tests: likely infra" next to the task ID, or in the Issues line.
- Got: "Verdict: intermittent… 1x TIMEOUT Test exceeded time limit". Only `fx-tests task <id>` shows the 1375/1854 header, and then it lists 1,376 failures by path, with the ones that have a traceback (`PermissionError: [WinError 5]`) not surfaced.
- Workaround: ran `fx-tests task --profiles --limit 0`, read the header counts, then grepped the task's live_backing.log for the traceback.

## `--bugs` prints nothing when no bug matches

- Command: `fx-tests test <path> --bugs`
- Expected: a "Bugs: none found" line.
- Got: the normal output with no Bugs section at all, so there is no telling "none" apart from "flag ignored".
- Workaround: searched Bugzilla REST by hand.
