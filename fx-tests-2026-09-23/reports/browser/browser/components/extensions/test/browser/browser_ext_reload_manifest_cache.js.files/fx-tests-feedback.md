## `test --bugs` is silent when no bug names the test
- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_reload_manifest_cache.js --bugs`
- Expected: a Bugs section, or an explicit "no bug names this test".
- Got: the normal `test` output with no bug section at all, so "no bugs" and "flag ignored" look identical.

## Did the failing runs include a given fix (push date vs run date)
- Question: are the failures dated 2026-09-14 on revisions after the fix that landed 2026-09-04?
- Got: `--history`/`--task-ids` date by run date; `task` prints a short hg rev only. The three 09-14 failures were retriggers on 2026-08-15 pushes, which looks like a recurrence until checked.
- Workaround: treeherder `push/?revision=` for the push time, lando `api/hg2git/firefox/<hg>` for the git hash, then `git merge-base --is-ancestor`. (Also: `curl https://hg.mozilla.org/integration/autoland/raw-file/<rev>/<path>`, as the brief says, returned HTTP 406 today; `git show <git hash>:<path>` via hg2git worked.)
- Could show: push date (and git hash) next to each task ID, or flag runs whose push is much older than the run.

## Which failing test in the job came first
- Question: which of this job's 44 failures started the vsync cascade?
- Got: `task --profiles`/`--messages` list failing tests alphabetically (e.g. Xh6MnwM2R3OFkWAeuPuZLA shows browser_ext_incognito_views.js first), not in run order.
- Workaround: loaded a profile and read its `test` markers. Run order (or a start time per failing test) in `task` would answer it directly.
