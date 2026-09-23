## Question: "does any bug name this test?" (fx-tests test --bugs)

- Command: `COLUMNS=250 fx-tests test toolkit/components/nimbus/test/unit/test_NimbusTestUtils.js --bugs`
- Expected: a Bugs section, saying "none" when no bug names the test.
- Got: the same output as without `--bugs`; no Bugs section at all, so "no bug" and "flag ignored" look identical.
- Workaround: `--json`, where `annotatedBugs` is `[]`; then a Bugzilla quicksearch, which found bug 1963964 and bug 1966517 with the same rejection stack in other tests. The default output could print "Bugs: none annotated" and, ideally, bugs whose summary carries the test's failure message.
