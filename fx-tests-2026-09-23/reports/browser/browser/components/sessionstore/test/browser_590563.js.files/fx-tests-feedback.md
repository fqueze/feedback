## `--bugs` prints no bug section at all

- Command: `fx-tests test browser/components/sessionstore/test/browser_590563.js --bugs`
- Expected: a list of bugs naming the test, or an explicit "no sheriff-annotated bugs found" line.
- Got: exactly the same output as without `--bugs` — no bug section, no "none" line, nothing on stderr. Bugzilla does have an open tracking bug naming the test (bug 1775613, "Intermittent browser/components/sessionstore/test/browser_590563.js | single tracking bug"), and `fx-tests intermittent --bug 1775613 --since 30 --tree all` reports no annotations. So "no annotations" is probably the truth, but the silence cannot be told apart from the flag being ignored.
- Workaround: `curl -sL "https://bugzilla.mozilla.org/rest/bug?summary=browser_590563.js"`.
