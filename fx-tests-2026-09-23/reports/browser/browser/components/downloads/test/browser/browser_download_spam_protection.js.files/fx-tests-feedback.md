## `--bugs` prints nothing about bugs

- Command: `fx-tests test browser/components/downloads/test/browser/browser_download_spam_protection.js --bugs`
- Expected: a Bugs section listing bug 1775779 ("Intermittent browser/components/downloads/test/browser/browser_download_spam_protection.js | single tracking bug"), or an explicit "no bug found" line.
- Got: the same output as without `--bugs` — no Bugs section and no "none" line, so no way to tell "no bug" from "flag ignored".
- Workaround: took the bug number from the manifest's `skip-if` comment.

## `--task-ids --limit 0` does not say which failure mode each task had

- Question: "which failing tasks are the most frequent failure mode (issue 2), so I load a profile of that one and not of the leak or the timeout?"
- Command: `fx-tests test <path> --task-ids --limit 0`
- Got: one flat list of 26 tasks by date, with no issue number per row. Had to run `--issue 3` and `--issue 4` separately and subtract.
- What would have answered it: the issue number (or the first message, cut) on each task row.
