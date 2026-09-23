# fx-tests feedback — browser_892956_destroyWidget_defaultPlacements.js

## `--bugs` prints nothing when there are no annotated bugs

- Command: `fx-tests test browser/components/customizableui/test/browser_892956_destroyWidget_defaultPlacements.js --bugs`
- Question: "is there a bug that names this test?"
- Expected: a line such as `Bugs: none annotated in the window`, next to the rest of the output.
- Got: the same output as without `--bugs`, with no bug section and nothing on stderr. It reads the same as "the lookup silently failed".
- Workaround: `--bugs --json` and read `annotatedBugs`, which is `[]`. Same for `browser_876926_customize_mode_wrapping.js` and `browser_1856572_ensure_Fluent_works_in_customizeMode.js`.
