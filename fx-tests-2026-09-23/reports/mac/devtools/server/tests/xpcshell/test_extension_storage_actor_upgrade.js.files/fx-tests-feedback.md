## `--bugs` with no annotated bug prints nothing

- Command: `fx-tests test devtools/server/tests/xpcshell/test_extension_storage_actor_upgrade.js --bugs`
- Expected: a line saying no sheriff-annotated bug names this test.
- Got: the ordinary output with no bugs section at all, identical to running without `--bugs`; only `--json` (`"annotatedBugs": []`) showed the query ran and found nothing.
- Workaround: `--json`, then a Bugzilla REST summary search to find the old closed bugs.

## Question: how many runs and failures per platform (why no Linux failures)?

- Command: `fx-tests test <path> --coverage --limit 0`
- Expected: per-platform totals next to "39 configs, 3 platforms: mac (9), windows (15), linux (15)".
- Got: config counts per platform, and per-config runs, but no per-platform run/fail totals; summing 39 rows by hand is error-prone.
- Workaround: `--json` and summing `coverage.configs[].runCount/failCount` by platform (mac 32/2,789, windows 9/3,577, linux 0/2,382).
