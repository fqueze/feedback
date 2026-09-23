## `fx-tests test <path> --bugs` found no bug although one names the test

- Command: `fx-tests test xpcom/tests/unit/test_nsIProcess.js --bugs`
- Expected: bug 1325609 ("Intermittent xpcshell timeout during xpcom/tests/unit/test_nsIProcess.js", ASSIGNED), which is also cited by the test's `skip-if`.
- Got: the same output as without `--bugs`; no bug section and no "none found" line, so it was unclear whether the flag did anything.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=test_nsIProcess'`.
