## `--bugs` with no matching bug prints nothing about bugs

- Command: `fx-tests test devtools/client/inspector/rules/test/browser_rules_completion-new-property_multiline.js --bugs`
- Expected: a "Bugs" section, saying "none found" when no bug names the test.
- Got: the same output as without `--bugs`, with no mention of bugs, so "no bug" looks the same as "flag ignored".
- Workaround: took the silence to mean no bug.
