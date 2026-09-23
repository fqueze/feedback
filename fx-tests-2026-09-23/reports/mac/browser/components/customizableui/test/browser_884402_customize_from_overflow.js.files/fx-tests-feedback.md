# fx-tests feedback (browser_884402_customize_from_overflow.js)

## `fx-tests test <path> --bugs` with no matching bug
- Command: `fx-tests test browser/components/customizableui/test/browser_884402_customize_from_overflow.js --bugs`
- Expected: a "Bugs" section, saying "none found" when there is none.
- Got: the same output as without `--bugs`, with no bug section at all, so "no bug" and "flag ignored" look the same.
- Workaround: searched Bugzilla's REST API by summary.

## Question: "is the upstream leaker also skipped where this test passes?"
- The decisive observation was that `browser_1856572_ensure_Fluent_works_in_customizeMode.js` is skipped on Mac 15.30 debug, where both `browser_876926` and this test pass. Getting it took one `--coverage --limit 0` per test, grepped for three configs.
- What would have answered it: `fx-tests test <path> --coverage --config <substring>` to filter the coverage table, or a way to show several tests' coverage side by side.
