## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test devtools/shared/commands/resource/tests/browser_resources_sources.js --bugs`
- Expected: a "Bugs" section, or a line saying no bug names this test.
- Got: exactly the same output as without `--bugs`; no mention of bugs at all, so "no bug" and "flag ignored" look identical.
- Workaround: none; assumed no bug.
