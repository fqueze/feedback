## Question: "which bug is about this test, including the one that fixed it?"

- Command: `fx-tests test browser/components/aboutlogins/tests/browser/browser_loginListChanges.js --bugs`
- Expected: a Bugs section listing bugs naming the test. At least bug 2072978 ("Wait for about:logins to be initialized in browser_loginListChanges.js", FIXED 2026-09-18), which is the landing that stopped the failure. Also old bug 1622494.
- Got: exactly the same output as without `--bugs`: no Bugs section, not even "none found". `--json` has `annotatedBugs: []` and no bug-search field at all.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=browser_loginListChanges&include_fields=id,summary,status,resolution"`.
- Could have shown: bugs whose summary names the test file, with status/resolution and resolved date. With `--history` showing a stop, point at a bug fixed on that date.

## `--since` takes days, not a date

- Command: `fx-tests test <path> --task-ids --limit 0 --since 2026-09-17`
- Got: `--since expects a non-negative integer, got "2026-09-17"`. `--day 2026-09-17` worked. Minor: `--day` takes a date and `--since` does not.
