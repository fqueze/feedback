## `test --bugs` prints nothing when it finds nothing

- Command: `fx-tests test browser/components/aboutwelcome/tests/browser/browser_aboutwelcome_multistage_default.js --bugs`
- Expected: a "Bugs" section, or a line saying none was found and where it looked. Bug 2046211 ("Intermittent browser/components/aboutwelcome/tests/browser/browser_aboutwelcome_multistage_default.js | single tracking bug") names the test, and `fx-tests intermittent --bug 2046211 --tree all --since 21` finds 2 annotations on it (mozilla-beta, mozilla-esr153).
- Got: exactly the output of the command without `--bugs`, exit 0 — indistinguishable from the flag being ignored.
- Workaround: Bugzilla REST search on the test file name (`/rest/bug?summary=<file name>`).
