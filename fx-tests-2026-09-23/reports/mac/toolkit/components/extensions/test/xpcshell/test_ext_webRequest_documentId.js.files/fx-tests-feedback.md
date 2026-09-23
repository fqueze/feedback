## `fx-tests test <path> --bugs` prints nothing when no bug names the test

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_webRequest_documentId.js --bugs`
- Expected: a line like `Bugs: none annotated`.
- Got: the same output as without `--bugs`; only `--json` shows `"annotatedBugs": []`, so "no bugs" and "the flag did nothing" look the same.
- Workaround: `--json`, plus a Bugzilla summary search.
