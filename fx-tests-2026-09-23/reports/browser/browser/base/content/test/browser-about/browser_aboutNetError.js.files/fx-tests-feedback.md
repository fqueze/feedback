# fx-tests feedback — browser_aboutNetError.js

## `test --bugs` prints nothing, and finds nothing that `intermittent --bug` finds

- Command: `fx-tests test browser/base/content/test/about/browser_aboutNetError.js --bugs`
- Expected: a bugs section naming bug 2057314 ("Intermittent browser/base/content/test/about/browser_aboutNetError.js | single tracking bug"), or at least an explicit "no annotated bugs in <window>" line.
- Got: the text output has no bugs section at all, so it is impossible to tell "none found" from "not queried"; `--json` has `"annotatedBugs": []`. Meanwhile `fx-tests intermittent --bug 2057314 --since 21 --tree all` lists 25 sheriff annotations on autoland, all naming this exact path, dated inside the 21-day window `test` reports on.
- Workaround: knew the bug number from the caller; used `intermittent --bug <N> --since 21 --tree all`.
- Also: `fx-tests intermittent --bug 2057314` with defaults said "no sheriff annotations ... between 2026-09-16 and 2026-09-22" and exited 2; the hint to widen was good, but the 7-day trunk default hides that every annotation of this bug in the window came from one day (2026-09-07).
