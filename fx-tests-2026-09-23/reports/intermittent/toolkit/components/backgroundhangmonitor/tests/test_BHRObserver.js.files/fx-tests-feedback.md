## Question: which assertion preceded each TIMEOUT? (test_BHRObserver.js)

- Command: `fx-tests test toolkit/components/backgroundhangmonitor/tests/test_BHRObserver.js` (and `--issue 4 --task-ids --json`)
- Expected: the Issues table to say that the 70 Linux "TIMEOUT Test exceeded time limit" runs had `[test_BHRObserver : 174] false == true` as their first failure message, i.e. the same failure as the 36 Android FAIL runs, reported as a timeout only because shutdown then hangs.
- Got: Issues lists "73x TIMEOUT Test exceeded time limit" and "36x FAIL [test_BHRObserver : 174]" as two separate failure modes; in `--json`, every `taskIds[]` entry of the timeout issue has `message: null`. Only `fx-tests task <id>` shows the line-174 message under the TIMEOUT.
- Workaround: ran `fx-tests task` on 12 of the timeout tasks one by one; all 12 had line 174 as the first message.
- What would have answered it: for TIMEOUT runs, the first TEST-UNEXPECTED-FAIL message logged before the timeout, in the Issues table (e.g. "73x TIMEOUT (after: [test_BHRObserver : 174] false == true)") and in `taskIds[].message`.
