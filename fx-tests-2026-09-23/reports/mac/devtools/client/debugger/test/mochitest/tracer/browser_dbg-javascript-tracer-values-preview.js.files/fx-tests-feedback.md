## Question: "is there a sheriff-annotated bug naming this test?"

- Command: `fx-tests test devtools/client/debugger/test/mochitest/tracer/browser_dbg-javascript-tracer-values-preview.js --bugs`
- Expected: a line such as "Annotated bugs: none" when no bug matches.
- Got: the same output as without `--bugs`, with no bugs section at all, so "no bug found" reads the same as "flag ignored" or "lookup failed".
- Workaround: `--bugs --json` and read `annotatedBugs`, which is `[]`.
