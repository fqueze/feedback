## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test devtools/client/inspector/markup/test/browser_markup_css_completion_style_attribute_02.js --bugs`
- Expected: a line such as "Bugs: none annotated" so the absence is an answer.
- Got: output identical to the run without `--bugs`, so I could not tell "no bug" from "the flag did nothing / the lookup failed silently".
- Workaround: a Bugzilla REST quicksearch for the test name (returned no bugs).
