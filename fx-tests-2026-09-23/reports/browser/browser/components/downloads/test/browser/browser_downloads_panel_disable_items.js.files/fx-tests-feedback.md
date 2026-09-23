## `test --bugs` prints nothing when there is no bug (browser_downloads_panel_disable_items.js)

- Question: is there a bug naming this test?
- Command: `fx-tests test browser/components/downloads/test/browser/browser_downloads_panel_disable_items.js --bugs`
- Expected: a "Bugs" section, saying "none" when empty.
- Got: exactly the output without `--bugs`; no line at all, so I could not tell "no bug" from "flag ignored".
- Workaround: `--json` showed `annotatedBugs: []`; then a Bugzilla REST search on the summary, which found 7 closed bugs (1747522, 1760628, 1766034, 1766075, 1775780, 1840312, 1968986). A "Bugs: none annotated" line, and the closed ones naming the test, would have answered it.
