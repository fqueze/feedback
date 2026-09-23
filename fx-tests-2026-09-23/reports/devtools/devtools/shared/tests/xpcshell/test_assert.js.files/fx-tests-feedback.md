# fx-tests feedback (test_assert.js)

## Question: "is there a bug naming this test?"
- Command: `fx-tests test devtools/shared/tests/xpcshell/test_assert.js --bugs`
- Expected: a Bugs section, or an explicit "no bug names this test" line.
- Got: the same output as without `--bugs` (summary, configs, issues), with no bugs section and no "none found" line, on stdout or stderr. It is impossible to tell "no bugs" from "flag ignored".
- Workaround: took it as "none".
