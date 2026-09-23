## `fx-tests test <path> --bugs` prints no bug section when there is none

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_webNavigation_bookmark_transitions.js --bugs`
- Expected: a "Bugs" section, or an explicit "no sheriff-annotated bug names this test" line.
- Got: the same output as without `--bugs`, nothing on stderr either; cannot tell "none found" from "the flag did nothing / the live query failed silently".
- Workaround: none; assumed no bug.
