# fx-tests feedback — test_DownloadLegacy.js

## A TEST-KNOWN-FAIL (todo_check) is reported as the failure message

```
fx-tests test toolkit/components/downloads/test/unit/test_DownloadLegacy.js
  Issues (first failure per run)
   4.   172x  FAIL    31 == true
fx-tests task QDGnHrqTQyiccW5G5G7-oA --profiles
  test_DownloadLegacy.js  TIMEOUT — 1 failing execution of 2
    31 == true
```

- Expected: the first *unexpected* message. On Windows (task Mx9lCKSxSXiNE4FhMcyPlQ) that is
  `File is empty: C:\...\xpcshell-download-test-legacy.txt` /
  `NS_ERROR_FILE_IS_LOCKED`; on the macOS timeouts it is `Test timed out`.
- Got: `31 == true` (and `62 == true`), which the per-test profile shows as `TEST-KNOWN-FAIL` /
  `TODO` markers from `todo_check` calls in `common_test_Download.js` — expected, and emitted
  by every run, passing or failing. The whole FAIL issue (172x) is labelled with a message that
  is not a failure, and the TIMEOUT headline in `task` is the same todo.
- Workaround: `fx-tests task <id> --profiles --messages` to see the other messages, then read
  the per-test profile's `TEST-KNOWN-FAIL` markers to learn that the headline was a todo.
- Suggestion: skip TEST-KNOWN-FAIL / TODO when choosing the headline and the Issues label.
