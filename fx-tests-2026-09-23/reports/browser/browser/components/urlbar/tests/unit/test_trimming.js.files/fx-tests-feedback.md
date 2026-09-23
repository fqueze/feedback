# fx-tests feedback (test_trimming.js)

## Question: which bug were this test's failures starred on?

- Command: `fx-tests test browser/components/urlbar/tests/unit/test_trimming.js --bugs`
- Expected: a Bugs section, or an explicit "no sheriff-annotated bug names this test".
- Got: the usual report with no Bugs section and no "none" line, so "no bug" and "the lookup did
  not run" look identical. The failures were starred on bug 2071618, whose summary names a sibling
  test (`test_autofill_functional.js`) that failed in the same jobs with the same message.
- Workaround: `fx-tests failures --message "<msg>" --tests` to find the sibling tests, then read
  the bug by hand. Could show: bugs annotated on this test's failing jobs, even when their summary
  names another test.

## Question: at which push did it start and stop failing?

- Commands: `fx-tests test <path> --history`, `--coverage --day <d>`.
- Expected: the first failing and the first passing-again revision for a step change.
- Got: per-day counts only. The boundaries needed Taskcluster task definitions (GECKO_HEAD_REV)
  and the Treeherder push API.
- Could show: for a step change, the last passing and first failing revision (and the reverse).

## `--task-ids --limit 0` prints the task list twice

- Command: `fx-tests test <path> --task-ids --limit 0`
- Got: the "Task IDs (114 jobs)" list, then a second, unheaded list of the same tasks in another
  order, starting again at 2026-09-03. It is unclear what the second list is (per issue row?).
