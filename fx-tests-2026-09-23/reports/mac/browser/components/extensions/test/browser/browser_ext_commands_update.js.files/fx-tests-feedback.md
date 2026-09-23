## `--bugs` prints nothing about bugs

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_commands_update.js --bugs`
- Expected: a Bugs section listing bugs naming the test, or an explicit "no bug names this test".
  Bugzilla has bug 1782997 ("Intermittent browser/components/extensions/test/browser/browser_ext_commands_update.js | single tracking bug", RESOLVED INCOMPLETE 2022).
- Got: the same output as without `--bugs`; no bug line at all, so "none" and "flag ignored" look identical.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=<file name>`.

## Question: which failing runs came before / after a given landing?

- To date the fix (bug 1378104, autoland push 278044) I needed each failing task's revision and push id.
- Command: `fx-tests test <path> --task-ids --limit 0` gives task ids grouped by run date only; I scripted
  Taskcluster `GET task/<id>` (payload.env.GECKO_HEAD_REV) and hg `json-pushes?changeset=` for 44 tasks.
- Could have shown: revision (short hash) and push id/date next to each task id, and a
  "newest failing push" / "oldest passing push after it" line. Run dates mislead here: 3 failures on
  2026-09-14 were backfills of 2026-08-15 revisions.

## Question: which other test failed in every job where this one failed?

- A victim's leaker is the test that fails in all of its jobs. I intersected
  `fx-tests test <victim> --task-ids` with `fx-tests test <suspect> --task-ids` by script (44 of 44).
- Could have shown: `fx-tests test <path> --co-failures` listing tests failing in the same jobs, with counts.
