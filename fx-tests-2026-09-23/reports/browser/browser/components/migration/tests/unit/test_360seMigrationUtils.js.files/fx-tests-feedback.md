## `--bugs` prints no bugs section at all

- Command: `fx-tests test browser/components/migration/tests/unit/test_360seMigrationUtils.js --bugs`
- Expected: a "Bugs" section, or an explicit "no sheriff-annotated bug names this test".
- Got: output identical to the run without `--bugs`; nothing on stderr either. Cannot tell "no bug" from "the lookup silently failed".
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?quicksearch=test_360seMigrationUtils.js` (empty) and a summary search.
