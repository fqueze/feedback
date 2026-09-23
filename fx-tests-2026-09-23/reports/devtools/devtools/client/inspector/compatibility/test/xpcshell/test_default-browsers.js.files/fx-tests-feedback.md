## `Failure details not recorded (likely Android or platform logging issue)` is misleading for Windows xpcshell

- Command: `fx-tests test devtools/client/inspector/compatibility/test/xpcshell/test_default-browsers.js`
- Expected: a hint at what a message-less xpcshell FAIL usually is. In `runxpcshelltests.py`, a
  test whose process exits non-zero with no failure output, on the retry path, gets the
  `test_end` message "Test failed or timed out, will retry" and the return code is never logged.
- Got: "likely Android or platform logging issue" on a Windows 10 debug job. That sent me looking
  for missing logs rather than for a non-zero exit code.
- Workaround: read `runxpcshelltests.py` at the revision to find out which conditions give FAIL
  with no message.

## `fx-tests task <id> --profiles` says nothing about why a failing test has no profile

- Command: `fx-tests task Hnmu5J7eRgKLG6Q8c9GCnQ --profiles`
- Expected: for a FAIL row with no message and no profile, something like "no per-test profile:
  the test's JS saw no failure (the process exited non-zero after the test)".
- Got: the row with no message and no profile line, next to other rows that had both.
- Workaround: grep live_backing.log for "profile uploaded in", and read head.js `_execute_test`.

## `--bugs` prints no bug section when it finds nothing

- Command: `fx-tests test <path> --bugs`
- Expected: "No bug names this test", or a list.
- Got: output identical to running without `--bugs`, so I could not tell whether the search ran.
