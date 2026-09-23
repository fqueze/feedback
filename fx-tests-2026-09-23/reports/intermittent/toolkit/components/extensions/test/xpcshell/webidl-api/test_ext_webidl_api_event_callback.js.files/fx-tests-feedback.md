## `test --bugs` prints no bug section and no "none found" line

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/webidl-api/test_ext_webidl_api_event_callback.js --bugs`
- Expected: a list of bugs naming the test (at least bug 1916563, the single tracking bug), or an explicit "no bugs found".
- Got: the same output as without `--bugs`; nothing mentions bugs, so "none" and "flag ignored" look the same.
- Workaround: `fx-tests intermittent --bug <N> --tree all --since 21` for the known bug.
