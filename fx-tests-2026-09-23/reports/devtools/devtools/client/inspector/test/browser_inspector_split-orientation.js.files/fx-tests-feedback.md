## `fx-tests test <path> --bugs` prints no bug section at all

- Command: `fx-tests test devtools/client/inspector/test/browser_inspector_split-orientation.js --bugs`
- Expected: a "Bugs" section listing bugs naming the test, or an explicit "no bugs found".
- Got: the same output as without `--bugs` (23 lines), no mention of bugs, not even "none". Cannot tell "no bug exists" from "flag ignored / lookup failed".
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?quicksearch=<test file name>` (returned no bugs).

