## `fx-tests test <path> --bugs` with no matching bug

- Command: `fx-tests test devtools/client/debugger/test/mochitest/integration/browser_dbg-features-source-tree.js --bugs`
- Expected: a line saying which bugs name the test, or that none do.
- Got: output identical to the run without `--bugs`, with no bug section at all. There was no way to tell "no bug" from "flag ignored".
- Workaround: took the absence as "none". An explicit "Bugs: none found naming this test" line would settle it.
