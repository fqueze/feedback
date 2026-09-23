## Question: does any bug name this test?

- Command: `COLUMNS=250 fx-tests test browser/components/places/tests/browser/browser_bookmark_titles.js --bugs`
- Expected: a "Bugs" section, saying "none" when no bug names the test.
- Got: the same output as without `--bugs`; no Bugs line at all, so "no bug" and "flag ignored" look identical.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?quicksearch=browser_bookmark_titles.js"` → `{"bugs": []}`.

## Question: which of this test's failing jobs also had the failure of another test (the leaker)?

- Command: `fx-tests task <id>` for each of the 9 task IDs from `fx-tests test <path> --task-ids`, then grep.
- Expected: one command listing, per failing job of a test, the other tests that failed in the same job (or a co-failure count), since "victim of" needs exactly that.
- Got: one `fx-tests task` call per job (9 calls).
- What would have answered it: `fx-tests test <path> --co-failures` (tests failing in the same jobs, with counts).
