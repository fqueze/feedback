# fx-tests feedback — test_group_overscroll_handoff.html

## `--bugs` prints nothing at all when no bug names the test

- Command: `fx-tests test gfx/layers/apz/test/mochitest/test_group_overscroll_handoff.html --bugs`
- Expected: a line such as `Bugs: none name this test`.
- Got: the usual report with no bugs section and nothing on stderr, so "no bug" and "flag ignored" look the same. I had to check `--json` (`"annotatedBugs": []`) to be sure.
- Workaround: `--json` and read `annotatedBugs`.
