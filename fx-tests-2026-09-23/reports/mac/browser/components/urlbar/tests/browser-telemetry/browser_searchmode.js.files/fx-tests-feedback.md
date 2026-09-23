## `--bugs` prints no line when it finds no bug

- Command: `fx-tests test browser/components/urlbar/tests/browser-telemetry/browser_searchmode.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test".
- Got: the same output as without `--bugs`, with no bug section at all, so "none found" and "flag ignored" look identical.
- Workaround: queried Bugzilla's REST API by summary myself.
