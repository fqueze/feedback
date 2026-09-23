## Question: which tasks ran this test on a given config of a try push (to open their resource-usage profiles)

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_webconsole_longstring_getter.js --task-ids --limit 0` (and the same with `--json`)
- Expected: the per-config table, with the task IDs (and ideally resource-usage profile URLs with `--profiles`) of the job runs behind each count, since `--task-ids` was passed.
- Got: only the counts table; `--task-ids` is silently ignored in `--test` mode, and the `--json` output has no task IDs either.
- Workaround: looked up the a11y-checks tasks with treeherder-cli / the Treeherder API.
- Why it matters: for a skipped test that passes on the push, the only profile evidence is the `test` marker in the resource-usage profile of the jobs that ran it; without the task IDs there is no way to get there from fx-tests.

