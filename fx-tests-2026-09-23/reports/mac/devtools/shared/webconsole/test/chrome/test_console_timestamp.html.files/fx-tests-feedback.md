## `--bugs` with no matching bug says nothing

- Command: `fx-tests test devtools/shared/webconsole/test/chrome/test_console_timestamp.html --bugs`
- Expected: a "Bugs" section, or a line such as "no bug names this test".
- Got: the same output as without `--bugs`, no Bugs section at all, so "no bug" and "flag
  ignored" look identical.
- Workaround: searched Bugzilla's REST API by summary.
