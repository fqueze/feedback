## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test toolkit/components/translations/tests/browser/browser_translations_translation_document_mutations.js --bugs`
- Expected: an explicit "Bugs: none found" line.
- Got: the same output as without `--bugs`; no Bugs section at all, so "no bug" and "flag ignored" look the same.
- Workaround: `--json` and checking that `annotatedBugs` is `[]`.
