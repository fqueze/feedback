## `--bugs` is silent when no bug names the test

- Command: `fx-tests test browser/components/tests/unit/test_distribution_idonly.js --bugs`
- Expected: a line such as "Bugs: none name this test".
- Got: output identical to the run without `--bugs`; only `--json` (`annotatedBugs: []`) showed the answer was "none" rather than "the flag was ignored".
- Workaround: `--json` and read `annotatedBugs`.

## `--issue <n>` does not number issues as the Issues list does

- Command: `fx-tests test browser/components/tests/unit/test_distribution_idonly.js --task-ids --limit 0 --issue 1 --since 2`
- Expected: tasks for issue 1 of the Issues list printed just above by the same command (`451x Unexpected exception NS_ERROR_UNEXPECTED ... getCharPref`).
- Got: header `Task IDs (issue 1: FAIL [test_idonly_distribution : 46] distribution.id should be s…)`, which is issue 2 of the list; `--issue 2` gave the NS_ERROR_UNEXPECTED one.
- Workaround: read the header of the Task IDs section to know which issue was picked.

