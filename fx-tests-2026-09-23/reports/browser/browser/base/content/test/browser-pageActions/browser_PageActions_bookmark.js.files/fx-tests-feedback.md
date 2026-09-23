## `--bugs` prints nothing when no open bug matches

- Command: `fx-tests test browser/base/content/test/browser-pageActions/browser_PageActions_bookmark.js --bugs` (same with the old `pageActions/` path)
- Expected: a Bugs section, even if it only says "no bug names this test", or lists closed ones (bug 1775979, the single tracking bug closed INCOMPLETE in 2026-06, names the old path).
- Got: the normal `test` output with no Bugs section and no line saying the search ran, so "no bugs" looks the same as "flag ignored".
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=browser_PageActions_bookmark`.
