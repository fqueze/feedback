## Question: at which push did this failure mode stop?

- Command: `fx-tests test <path> --task-ids --limit 0 --issue 2` (and `--json`, whose `taskIds[]` has taskId, day, message only).
- Expected: the revision (and push time) of each failing task, or the last and first revisions around the point where one issue disappears while another takes over.
- Got: days only. `--history` showed the test's rate unchanged while issue 2 went to zero and issue 1 rose on 2026-09-08, but nothing named the push.
- Workaround: a script over ~440 task IDs, reading `GECKO_HEAD_REV` from the Taskcluster task definition, mapping it to git with `lando.moz.tools/api/hg2git`, then `git merge-base --is-ancestor`. That found bug 2070008 as the boundary (96/0 before/after for issue 2, 22/318 for issue 1).
- Could show: the revision per task ID in `--task-ids`, and for `--history` a per-issue daily split, with the last/first revision around a disappearing issue.
