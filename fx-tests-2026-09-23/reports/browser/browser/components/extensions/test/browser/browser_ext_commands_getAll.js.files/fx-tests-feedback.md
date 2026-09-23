# fx-tests feedback

## `--bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_getAll.js --bugs`
- Expected: a line such as "Bugs: none name this test" (or the list).
- Got: the same output as without `--bugs`, with no bugs section at all, so "no bug found" looks the same as "the flag was ignored".
- Workaround: none; assumed no bug.
