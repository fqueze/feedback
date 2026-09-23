## `test --bugs` prints no bug section when there is none

- Command: `fx-tests test devtools/client/inspector/test/highlighter/browser_inspector_highlighter-cssshape_iframe_01.js --bugs`
- Expected: a "Bugs" section, or a line saying no bug names the test.
- Got: the same output as without `--bugs`, ending at the Issues list. Could not tell "no bug found" from "flag ignored"; checked Bugzilla quicksearch by hand.
