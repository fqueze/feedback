## `fx-tests test <path> --bugs` prints nothing about bugs when there are none

- Command: `fx-tests test devtools/client/application/test/browser/browser_application_panel_viewsource-service-worker.js --bugs`
- Expected: a Bugs section, even if it only says that no bug names the test.
- Got: exactly the same output as without `--bugs`, with no Bugs line. So "no bug found" looks the same as "the flag was ignored".
- Workaround: a Bugzilla REST quicksearch on the test file name, which returned `[]`.
