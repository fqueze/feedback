## `fx-tests test <path> --bugs` shows nothing about bugs

- Command: `fx-tests test browser/components/customizableui/test/browser_901207_searchbar_in_panel.js --bugs`
- Expected: a Bugs section listing bugs naming the test (Bugzilla quicksearch finds bug 985413), or an explicit "no bug names this test".
- Got: the same output as without `--bugs`, nothing on stdout or stderr about bugs.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?quicksearch=<test file name>`.
