## `fx-tests test <path> --bugs` says nothing when no bug names the test

- Command: `fx-tests test browser/components/aboutwelcome/tests/browser/browser_aboutwelcome_campaign_actions.js --bugs`
- Expected: a `Bugs` section, reading "none" when no bug names the test.
- Got: the ordinary `fx-tests test` output with no bug section at all, so "no bug" and "flag ignored" look the same.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?quicksearch=<file name>"`.

## Question: which other tests fail in the same jobs as this one?

- Needed to tell whether this test leaks into later tests of its manifest (configurable_ui and fxa_signin_flow failed after it in both jobs I opened).
- Command: `fx-tests test <path> --task-ids --limit 0` for three tests, then scraped the task IDs with grep and intersected them with `comm`.
- What could show it: a `--co-failures` view on `fx-tests test`: for each other test failing in the same jobs, "failed in N of this test's M failing jobs, and N of its own K failing jobs". Here: configurable_ui 27/28, fxa_signin_flow 27/27.
