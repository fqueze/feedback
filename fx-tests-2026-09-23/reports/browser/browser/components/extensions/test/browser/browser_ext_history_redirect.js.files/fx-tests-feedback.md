## Question: which tests in one manifest share a cascade failure message, in manifest order

Command: `fx-tests failures --harness mochitest --message 'waiting for vsync to be disabled' --tests --limit 0`
Expected: all tests behind the message, or at least all in one directory (`--path` combined with `--message`).
Got: the list is capped at 50 with "… 93 more tests (--json for all of them)" even with `--limit 0` (which apparently limits rows, not tests).
Workaround: `--json` and a python filter on `components/extensions`.
What the output could show: honor `--limit 0` for the tests list too; and for a harness-wide post-test check, the first test in manifest order per manifest would point straight at the leaker.

## Question: is this one failure mode, when messages differ only by a UUID

Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_execute_page_action.js`
Expected: "uncaught rejection: PageActions: No anchor node for _<uuid>_" grouped as one issue with its count (~46).
Got: one 1x row per UUID, truncated after 9 rows, next to "3x TIMEOUT" at the top, so the timeout looks like the main failure mode.
Workaround: `--limit 0` and `sed`/`uniq` to normalise UUIDs.
What the output could show: normalise UUIDs/hex ids/ports before grouping issues.

## Question: did these failures run on revisions before or after a fix landed

Command: `fx-tests test <path> --task-ids --limit 0` then `fx-tests task <id>` per task for the revision, then the Treeherder push API for each push date.
Expected: the failing task list to carry the revision and its push date (3 failures dated 2026-09-14 were on pushes from 2026-08-15, which reads as a regression until checked).
Got: the day the task ran only; no revision in `--task-ids` (text or JSON).
Workaround: `fx-tests task <id>` per task, then `curl https://treeherder.mozilla.org/api/project/autoland/push/?revision=<rev>`.
What the output could show: revision and push date per task in `--task-ids`, and `--history` bucketed by push date rather than run date.
