## Bugs naming the test (question: "is there a bug for this test?")

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_menus_viewType.js --bugs`
- Expected: a Bugs section, either listing bugs or saying none were found.
- Got: the same output as without `--bugs`, with no Bugs section and no "none found" line, so I could not tell whether it searched. Bugzilla has five bugs whose summary names the test (1513079, 1569727, 1575646, 1727174, 1775564, all resolved; 1775564 closed INCOMPLETE on 2026-09-14).
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=browser_ext_menus_viewType'`. Printing the resolved ones too, marked as resolved, would help: a tracking bug closed a week ago still says who looked at the test.
