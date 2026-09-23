## Finding a profile that holds a passing run of this test

- Question: "a per-test profile, on Linux (or Windows), whose browser session ran browser_tab_groups_list.js and passed it", to compare against the failing runs.
- Commands: `fx-tests flaky <dir> --harness mochitest --here-only` to list the failing neighbours, then `fx-tests test <neighbour> --task-ids --limit 0 | grep linux…` for each, then `fx-tests task <id> --profiles`, then loading the profile to check the test really ran in it (the first Windows one did not: -vt configs skip it).
- Expected: something like `fx-tests test <path> --passing-profiles [--config …]`, listing failure profiles of later tests in the same manifest and job, where this test passed.
- Got: about 8 commands and one wasted profile load.
