## `test --bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_webRequest_error_after_stopped_or_closed.js --bugs`
- Expected: a "Bugs" section, saying "none" when no sheriff-annotated bug names the test.
- Got: output byte-identical to the run without `--bugs`, exit 0, nothing on stderr; can't tell "no bug" from "the flag did nothing / the query failed".
- Workaround: Bugzilla REST `quicksearch=<test file name>` (returned no bug).
