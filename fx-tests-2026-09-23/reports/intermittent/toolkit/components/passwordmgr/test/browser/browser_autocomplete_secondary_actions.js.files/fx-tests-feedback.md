## Question: did any failure happen after landing X? (push/revision of each failing task)

- Command: `fx-tests test <path> --task-ids --limit 0 --since 6`
- Expected: each failing task with its revision (and ideally push id/time), so "last failing push vs. the fix's push" can be read off directly.
- Got: task ids grouped by day only. I ran `fx-tests task <id> | sed -n 2p` on 8 tasks to get revisions, then `curl hg.mozilla.org/.../json-pushes?changeset=<rev>` per revision to order them against the fix push (hg.mozilla.org then timed out for a while).
- Could have shown: revision + push time column in `--task-ids` output.

## `--since` takes a day count, not a date

- Command: `fx-tests test <path> --task-ids --limit 0 --since 2026-09-17`
- Expected: accepted, since every output prints dates.
- Got: `--since expects a non-negative integer, got "2026-09-17"`.
- Workaround: `--since 6`. Minor; accepting a date too would help.
