## `fx-tests test <path> --bugs` prints no bug section at all

- Command: `fx-tests test browser/components/urlbar/tests/browser/browser_stop_pending.js --bugs`
- Expected: a "Bugs" section listing bugs naming the test (Bugzilla has 9, including the closed
  single tracking bug 1775818), or an explicit "no open bug names this test".
- Got: the same output as without `--bugs`; `--json` has `annotatedBugs: []` and no `bugs` key.
  Silence cannot be told apart from the flag being ignored.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=browser_stop_pending.js"`.
