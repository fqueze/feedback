# fx-tests feedback (marFailurePartialZucchini.js)

## `--bugs` prints nothing distinguishable when no bug is found

- Command: `fx-tests test toolkit/mozapps/update/tests/unit_update_binary/marFailurePartialZucchini.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test".
- Got: output identical to the run without `--bugs`; nothing says whether a search was made.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=marFailurePartialZucchini` (returned no bug).

## Rate before the window's first day

- Question: "did this test fail before landing X, which is 5 days before the window starts?"
- Command: none in fx-tests; the window is 2026-08-31 onward. Workaround: Treeherder `/api/project/mozilla-central/push/` and `/jobs/?push_id=` to list 40 macOS 15 xpcshell task IDs on either side of the landing, then `fx-tests task <id>` on each and grep for `unit_update_binary` (see pre-reland-jobs.txt / post-reland-jobs.txt).
- What would have shown it: `fx-tests task` works on older tasks, so a `fx-tests test <path> --before <date>` or `fx-tests jobs --config <c> --push-range` that lists task IDs for a config and date range would have made this one command.
