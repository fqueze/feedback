## `fx-tests test <path> --bugs` prints nothing when there is no bug (browser_bug631374_tags_selector_scroll.js)

- Question: does any sheriff-annotated bug name this test?
- Command: `fx-tests test browser/components/places/tests/browser/browser_bug631374_tags_selector_scroll.js --bugs`
- Expected: a "Bugs" section, or a line such as "No annotated bugs name this test".
- Got: exactly the output without `--bugs`, so it is impossible to tell "no bug" apart from "the flag did nothing / the lookup failed".
- Workaround: `--bugs --json` and reading `annotatedBugs: []`, then a Bugzilla REST search on the file name.
