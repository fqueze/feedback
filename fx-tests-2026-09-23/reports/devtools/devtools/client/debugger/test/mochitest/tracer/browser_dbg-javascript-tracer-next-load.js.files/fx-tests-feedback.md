## `fx-tests test <path> --bugs` prints nothing when no bug names the test

- Command: `fx-tests test devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer-next-load.js --bugs`
- Expected: an explicit line such as `Bugs: none name this test`.
- Got: the same output as without `--bugs`, with no bugs section at all, so "no bug" looks the same as "the flag was ignored".
- Workaround: none needed beyond trusting the silence; cost a second look to be sure.
