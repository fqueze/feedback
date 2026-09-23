## `--bugs` is silent when no bug matches

- Command: `fx-tests test browser/base/content/test/contextMenu/browser_copy_link_to_highlight_viewsource.js --bugs`
- Expected: a "Bugs" section, saying "none found" when no bug names the test.
- Got: the normal `test` output with no bugs section at all, so "no bug" looks the same as "the flag was ignored".
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?quicksearch=<test file name>"` returned `{"bugs":[]}`.
