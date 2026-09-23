## Per-config counts for one failure mode

- Question: "on which configs does the TIMEOUT mode happen, and on which the FAIL (`Clicked span inside panel...`) mode?" The two modes have disjoint platforms: mac and Linux opt for the timeouts, tsan/asan for the fails.
- Command: `fx-tests test toolkit/components/passwordmgr/test/browser/browser_autocomplete_import.js` — its "Failing configurations" table merges both modes; `--issue 2` only filters the `--task-ids` list.
- Expected: the per-config table, restricted to the issue when `--issue N` is given (or an issue column in it).
- Workaround: `fx-tests test <path> --task-ids --limit 0 --issue 2 | rg -o 'test-[^ ]+' | sed 's/-[0-9]*$//' | sort | uniq -c`, once per issue.
