## `fx-tests test <path> --bugs` prints no bugs section when none is found

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_tab_splitview_tab_order.js --bugs`
- Expected: a "Bugs" section, or a line saying no sheriff-annotated bug names this test (and ideally closed ones, e.g. bug 2022316, RESOLVED INCOMPLETE, the test's "single tracking bug").
- Got: exactly the same output as without `--bugs`, nothing on stderr, exit 0. Impossible to tell "no bug" from "flag ignored / query failed".
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=browser_tab_splitview_tab_order`.
