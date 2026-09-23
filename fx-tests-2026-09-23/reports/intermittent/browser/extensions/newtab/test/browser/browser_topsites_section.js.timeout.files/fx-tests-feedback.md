## Per-config counts of one failure mode

- Command: `fx-tests test browser/extensions/newtab/test/browser/browser_topsites_section.js --task-ids --limit 0 --issue 2`
- Expected: the "Failing configurations" table restricted to issue 2 (the timeout), or a per-config count next to the task list.
- Got: the per-config table stays the all-modes one (tsan 148 fails, mostly another failure mode), and the timeout's own distribution is only in the task list.
- Workaround: counted configs from the task-list text with awk.

## The Issues label is not the logged message

- Command: `fx-tests test <path>` Issues list
- Expected: the verbatim failure message, so it can be quoted and searched in the profile.
- Got: `TIMEOUT Test exceeded time limit`, while the log, `fx-tests task` and the profile's TEST-UNEXPECTED-FAIL marker all say `Test timed out`. Searching the profile for the Issues text finds nothing.
- Workaround: took the message from `fx-tests task <id>`.
