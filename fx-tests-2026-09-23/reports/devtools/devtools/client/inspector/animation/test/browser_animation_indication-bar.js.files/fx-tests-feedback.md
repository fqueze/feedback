## Finding a skip-removed test's failure on a try push

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids` then `rg indication-bar` on the output.
- Expected: the test's row (it failed 6/6 on a11y-checks-6), or an explicit "not in the failures".
- Got: nothing; the row was hidden behind the default limit ("… 25 more (--limit 0 for all)"). `--all-jobs` (a ~10 min read) also truncates it the same way. `--all-jobs --test <path>` then showed "failed 3" per config, but with no task IDs, message, or profile URLs even with `--task-ids --json`, so a third run was needed.
- Workaround: `fx-tests try <rev> --profiles --task-ids --limit 0`.
- Question it could have answered: "did this one test fail on this push, how, and where are its profiles?" `--test <path>` should honour `--task-ids`/`--profiles` and print the messages; the default listing could say when a truncated tail is being cut.
