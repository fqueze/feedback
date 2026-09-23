## `fx-tests test <path> --bugs` is silent when no bug names the test

- Command: `fx-tests test devtools/client/aboutdebugging/test/browser/browser_aboutdebugging_addons_popup_picker.js --bugs`
- Expected: a line such as "Bugs: none name this test".
- Got: the usual output with no bug section at all, indistinguishable from the flag being ignored.
- Workaround: `--bugs --json` and reading `annotatedBugs: []`.

