# fx-tests feedback (browser_bug2044829.js)

## Question: which bugs name this test?

- Command: `fx-tests test gfx/layers/apz/test/mochitest/browser_bug2044829.js --bugs`
- Expected: a Bugs section, saying "none found" when there is none.
- Got: output identical to the command without `--bugs`, with no Bugs section and no "none" line, so I could not tell "no bug" from "flag ignored".
- Workaround: a Bugzilla REST quicksearch for the file name (`/rest/bug?quicksearch=browser_bug2044829.js`), which returned no bugs.
