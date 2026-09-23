## Message-less failure: misleading label, and not findable under the same text in `failures`

- Command: `fx-tests test devtools/client/application/test/xpcshell/test_ui_reducer.js`
- Expected: a neutral label for a failure with no failure message, e.g. "no failure message (harness failed it on the exit code?)".
- Got: `Failure details not recorded (likely Android or platform logging issue)`. The only failure was on Windows (test-windows10-64-2009-qr/debug-xpcshell), so the Android hint pointed the wrong way.
- Then: `fx-tests failures --harness xpcshell --message "Failure details not recorded"` and `--message "no message recorded"` both returned "No failure matched". The same failure is listed as `(no message recorded)` there, but `--message` does not match that placeholder text.
- Workaround: `fx-tests failures --harness xpcshell --path devtools/client/application --tests`.

## `task`: say what a failure with no message means

- Command: `fx-tests task Md90xuCaQ5GGk9qMZriLMw --messages`
- Question: "why did this test fail when all its checks passed?"
- Got: `FAIL — 1 failing execution of 2` and no message line.
- Could show: that no failure line was logged, so the harness failed it on the process exit code, and that runxpcshelltests.py does not log that code on its retry path. That would save reading the harness to rule out a hidden failure line.

## `errors --task-ids --limit 0` still cuts the task list

- Command: `fx-tests errors --harness xpcshell --message "nsNotifyAddrListener released from static" --day 2026-09-14 --group-by message --task-ids --limit 0`
- Question: "which jobs logged this message?"
- Expected: all 9 task IDs.
- Got: 8 IDs and `… 1 more`. `--limit 0` does not apply to the task-ID list.
- Workaround: `--json`, reading `.rows[].taskIds`.
