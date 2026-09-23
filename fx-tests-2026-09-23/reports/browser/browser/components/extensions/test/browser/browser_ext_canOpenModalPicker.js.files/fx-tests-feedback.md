## `--bugs` prints nothing when no bug matches

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_canOpenModalPicker.js --bugs`
- Expected: a "Bugs" section, or a line saying no sheriff-annotated bug names this test.
- Got: exactly the same output as without `--bugs`, nothing on stderr either, so it is impossible to tell "no bug" from "the flag was ignored / the query failed". (Bugzilla has bug 1949651, "Intermittent ... browser_ext_canOpenModalPicker.js | single tracking bug", RESOLVED FIXED 2026-08-26 — presumably excluded because closed, but nothing says so.)
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=browser_ext_canOpenModalPicker`.
