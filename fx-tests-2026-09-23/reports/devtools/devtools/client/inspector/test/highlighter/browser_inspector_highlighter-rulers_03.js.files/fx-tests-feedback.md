## `--bugs` with no matching bug prints nothing about bugs

- Command: `fx-tests test devtools/client/inspector/test/highlighter/browser_inspector_highlighter-rulers_03.js --bugs`
- Expected: a Bugs section, saying "no bug names this test" when there is none.
- Got: exactly the default output (verdict, failing configs, issues), with no line about bugs, so "no bug" reads the same as "flag ignored".
- Workaround: took the absence as "none"; the todo list for this run also lists no bug.
