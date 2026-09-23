## `fx-tests test <path> --bugs` prints nothing about bugs

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_navigate_through_urls_origin_attributes.js --bugs`
- Expected: a Bugs section, listing bug 1933950 ("Intermittent ... browser_navigate_through_urls_origin_attributes.js | single tracking bug", RESOLVED INCOMPLETE by BugBot in 2024), or a line saying none was found.
- Got: the same output as without `--bugs`, with no bug section and no "none found" line, so "no bug" and "flag ignored" look identical.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=browser_navigate_through_urls_origin_attributes"`.
