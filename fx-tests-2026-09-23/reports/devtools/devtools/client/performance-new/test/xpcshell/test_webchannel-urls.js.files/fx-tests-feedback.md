## Issue label for a no-message xpcshell FAIL is misleading

- Command: `fx-tests test devtools/client/performance-new/test/xpcshell/test_webchannel-urls.js`
- Expected: something pointing at what the failure was — here every assertion passed and the xpcshell process exited with a non-zero return code (the harness's retry path replaces the return code with "Test failed or timed out, will retry").
- Got: `FAIL  Failure details not recorded (likely Android or platform logging issue)` on a Windows desktop job; `fx-tests task <id> --json` has `messages: []`.
- Workaround: downloaded live_backing.log and read the replayed full log and `runxpcshelltests.py`.
- Could show: "no failing assertion in the replay: non-zero exit / abnormal shutdown", or the last lines of the replayed output (here `[Link Monitor #1] ... released from static ctor/dtor`).

## Question: which other tests fail the same way (no message, same line in the replayed log)?

- Command: `fx-tests errors --harness xpcshell --day <D> --message "nsNotifyAddrListener released from static" --group-by test --task-ids`, one day at a time (5 runs), then `fx-tests task <id>` per task to learn the config.
- Expected: one command over the window giving the tests and configs carrying the line; `--config` is listed in `errors --help`.
- Got: per-day only, and `--config` refused ("the file records no job names"), although `--task-ids` names the tasks from which the config is derivable.
