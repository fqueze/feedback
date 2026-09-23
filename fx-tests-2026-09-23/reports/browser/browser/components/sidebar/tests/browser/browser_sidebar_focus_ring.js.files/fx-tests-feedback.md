## `--bugs` prints nothing when there is no bug

- Command: `fx-tests test browser/components/sidebar/tests/browser/browser_sidebar_focus_ring.js --bugs`
- Expected: a line such as "Bugs: none found" when no sheriff-annotated bug names the test.
- Got: the normal output with no bugs section at all, which reads the same as the flag being ignored.
- Workaround: confirmed with a Bugzilla REST quicksearch.
