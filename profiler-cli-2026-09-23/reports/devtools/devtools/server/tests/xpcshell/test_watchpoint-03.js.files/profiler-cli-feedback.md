## Searching for a marker's displayed label matches nothing

- Command: `profiler-cli thread markers --session <s> --search "TIMEOUT —" --list --limit 0 --json` on a resource-usage profile (JOcVulCfRyynVd-aExDW1g).
- Expected: the 1060 `test` markers that `--list` displays as `TIMEOUT — <test path>`.
- Got: 0 markers. The displayed text is built from the `status` and name fields, so a substring spanning both (`TIMEOUT —`) matches no single field. Nothing says so.
- Workaround: `--search "name:test" --group-by field:status` for the count, and `--search TIMEOUT` (which also matches unrelated INFO lines) for the list.
- Question it was for: "how many tests timed out in this job, and over what start-time range". The group-by gave the count, but not the start-time range. A min/max start per group would have answered it.
