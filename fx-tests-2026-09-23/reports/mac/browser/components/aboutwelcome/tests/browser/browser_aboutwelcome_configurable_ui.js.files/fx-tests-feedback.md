## Question: which bugs are sheriffs starring this test's failures on?

- Command: `fx-tests test browser/components/aboutwelcome/tests/browser/browser_aboutwelcome_configurable_ui.js --bugs`
- Expected: a section listing the annotated bugs (the `--help` text says `--bugs` gives "The sheriff-annotated bugs naming this test"), or a line saying none were found.
- Got: the same text as without `--bugs` — no bugs section, no "none" line, on stdout or stderr. The data is there: `--json` has `annotatedBugs: [{bugId: 2073711, count: 2, …}, {bugId: 1784548, count: 1, …}]`. For `browser_aboutwelcome_campaign_actions.js`, `annotatedBugs` is `[]`, and the text output gives no hint of that either, so "no bugs" and "the flag did nothing" look the same.
- Workaround: `--bugs --json | python3 -c '…d["annotatedBugs"]…'`.
