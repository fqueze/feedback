## `test --bugs` prints nothing when there are no annotated bugs

- Command: `fx-tests test browser/components/extensions/test/xpcshell/test_ext_menu_startup.js --bugs`
- Expected: a line such as "Annotated bugs (2026-09-16..2026-09-22): none", so that "no bug" is distinguishable from "the lookup failed" or "the section was cut off".
- Got: exactly the same output as without `--bugs`, exit 0; the progress lines on stderr ("Ranking annotated bugs…", "Reading 720 bug summaries…") show it ran, but stdout has no trace of the result.
- Workaround: `--bugs --json` and read `annotatedBugs: []`; then a Bugzilla REST summary search to find the (closed) tracking bug 1903651.
- Question the default output could have answered: "is there a bug for this test, open or not?" — printing the closed tracking bug with its resolution would also have saved the Bugzilla query.
