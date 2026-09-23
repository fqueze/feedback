## `--bugs` prints nothing when no bug is annotated

- Command: `fx-tests test browser/components/profiles/tests/unit/test_delete_last_profile.js --bugs`
- Expected: a line such as "Annotated bugs: none in the last N days".
- Got: the same output as without `--bugs`; only `--json` showed `"annotatedBugs": []`. Indistinguishable from the flag being ignored or the query failing.
- Workaround: `--json` and read `annotatedBugs`; Bugzilla quicksearch found bug 2040005 (for failure mode 2, with no recent annotations).

## Question: which configs does one failure mode happen on?

- Command: `fx-tests test <path> --task-ids --issue 1 --limit 0 | grep -o "test-[^ ]*" | sed 's/-[0-9]*$//' | sort | uniq -c`
- Expected: a per-config count for each row of the Issues block, or for `--issue <n>`. Here that separates the macOS-only failure mode 1 from the all-platform mode 2.
- Got: a per-day list of task IDs, and I had to aggregate it myself (run once per issue).
- Suggestion: add a config breakdown line under each Issues row, or a per-config table when `--issue` is given.
