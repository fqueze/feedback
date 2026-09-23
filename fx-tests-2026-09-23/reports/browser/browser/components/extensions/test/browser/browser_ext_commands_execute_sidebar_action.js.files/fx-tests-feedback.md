## Whether a failure happened on a revision after a fix landed

- Question: the vsync failures stopped after 2026-09-04, when the leaker's fix landed, but 3 reappear on 2026-09-14: are those on revisions with the fix?
- Command: `fx-tests test <path> --history`, then `fx-tests task IT0S_eCQRJ-SkMnxZ3h6jA`.
- Got: per-day counts by run date, and the job's revision (`autoland a78ff5819100`), but not its push date.
- Workaround: `curl https://treeherder.mozilla.org/api/project/autoland/push/?revision=a78ff5819100` — pushed 2026-08-15, so a backfill/retrigger of an old push. Showing the push date next to the revision in `task`, or flagging runs whose push is much older than the run date in `--history` / `--task-ids`, would have answered it.
