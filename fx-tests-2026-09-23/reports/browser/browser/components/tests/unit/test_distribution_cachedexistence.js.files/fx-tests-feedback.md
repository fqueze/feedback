## `test --bugs` prints nothing when no bug is found

- Command: `fx-tests test browser/components/tests/unit/test_distribution_cachedexistence.js --bugs`
- Expected: a line such as "Annotated bugs: none", so "no bug" is distinguishable from "the flag did nothing / the query failed".
- Got: the same output as without `--bugs`; only `--json` showed `"annotatedBugs": []`.
- Workaround: `--json` plus a Bugzilla REST quicksearch.
