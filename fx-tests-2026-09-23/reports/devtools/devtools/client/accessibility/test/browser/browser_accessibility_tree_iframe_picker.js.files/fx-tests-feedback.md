## `test --bugs` prints nothing when no bug is annotated

- Command: `fx-tests test devtools/client/accessibility/test/browser/browser_accessibility_tree_iframe_picker.js --bugs`
- Question: "is any bug annotated with this test's failures?"
- Expected: a Bugs section, saying "no sheriff-annotated bug names this test" when there is none.
- Got: the same output as without `--bugs`, with no Bugs section at all. I could not tell "no bug" from "the flag did nothing".
- Workaround: `--bugs --json` and a script reading `annotatedBugs` (it was `[]`), then a Bugzilla REST search by summary.
