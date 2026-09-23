## `--bugs` gives no answer when no bug matches

- Command: `fx-tests test browser/components/urlbar/tests/browser-newtab/browser_searchModeSwitcher.js --bugs`
- Expected: a "Bugs" section, or a line saying no bug names this test.
- Got: exactly the same output as without `--bugs`, with no bug section and no "none found" line. I could not tell "searched, found nothing" apart from "flag ignored".
- Workaround: queried the Bugzilla REST API directly (`/rest/bug?quicksearch=...`), which returned no bugs.
