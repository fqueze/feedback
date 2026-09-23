## `fx-tests test <path> --bugs` prints no bug section

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_multiselect_tabs_unload_with_beforeunload.js --bugs`
- Expected: a "Bugs" section, even if it says none is open; Bugzilla has two closed tracking bugs naming the test (1926186 FIXED, 1940604 INCOMPLETE).
- Got: the same output as without `--bugs`, with no line about bugs at all, so "no bug" and "flag ignored" look identical.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=<test file name>'`.
