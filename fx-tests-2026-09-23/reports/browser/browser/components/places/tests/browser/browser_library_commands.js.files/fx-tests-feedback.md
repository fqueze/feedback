## `--bugs` prints nothing when there are no bugs
- Command: `fx-tests test browser/components/places/tests/browser/browser_library_commands.js --bugs`
- Expected: a line such as "Annotated bugs: none" after the Issues section.
- Got: the same output as without `--bugs`, with no bugs section at all, so "no bugs" looked like "flag ignored".
- Workaround: `--json` and read `annotatedBugs` (`[]`).
