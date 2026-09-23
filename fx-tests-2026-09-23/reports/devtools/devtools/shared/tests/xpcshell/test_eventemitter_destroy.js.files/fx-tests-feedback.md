## `--bugs` prints nothing when no bug matches (same as already logged by other reports)
- Command: `fx-tests test devtools/shared/tests/xpcshell/test_eventemitter_destroy.js --bugs`
- Got: output identical to without `--bugs`; cost two extra calls to confirm it was not ignored.
