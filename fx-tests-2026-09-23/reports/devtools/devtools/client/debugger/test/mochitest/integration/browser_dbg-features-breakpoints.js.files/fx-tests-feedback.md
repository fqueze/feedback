# fx-tests feedback (browser_dbg-features-breakpoints.js)

## `test --bugs` says nothing when it finds no bug

- Command: `fx-tests test devtools/client/debugger/test/mochitest/integration/browser_dbg-features-breakpoints.js --bugs`
- Expected: a `Bugs` section, saying "none" when no bug names the test.
- Got: the same output as without `--bugs`, so "no bug found" and "flag ignored" look the same.
- Workaround: none; read it as no bug. The actual cause (bug 2070924) names another test, which `--bugs` cannot know.
