## browser_rules_nested_at_rules.js (2026-09-22)

- Command: `fx-tests test devtools/client/inspector/rules/test/browser_rules_nested_at_rules.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test" line.
- Got: the same output as without `--bugs`, with no bugs section and no line saying none was found, so "no bug" and "flag ignored" look the same.
- Workaround: took it as "no bug".
