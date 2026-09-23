## `fx-tests test <path> --bugs` with no matching bug says nothing

- Command: `fx-tests test devtools/client/inspector/fonts/test/browser_fontinspector_editor-variable.js --bugs`
- Expected: a "Bugs" section, or a line such as "No bug names this test".
- Got: the same summary as without `--bugs` (verdict, per-config table, issues), and no Bugs section at all, so it is unclear whether the search ran and found nothing, or did not run.
- Workaround: took the absence as "no bug".
