## Which jobs ran this test on a try push, and their profile URLs

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/webconsole/test/browser/browser_console_origin_filters.js --all-jobs --task-ids --profiles` (also with `--json`)
- Expected: the task IDs (and resource-usage profile URLs) of the job runs counted in each config row, since `--task-ids`/`--profiles` were passed.
- Got: only the per-config counts table; `--task-ids` and `--profiles` are silently ignored in `--test` mode, and the JSON has no task IDs either.
- Workaround: grepped the full `fx-tests try <rev> --task-ids` output for task lines of the same config (only works because those jobs failed for other tests; the passing jobs' IDs are unreachable this way).

## Job counts differ between `--test` with and without `--all-jobs`

- Command: `fx-tests try 2888bcab0070... --test <path>` vs the same with `--all-jobs`.
- Expected: the same `jobs` column (it is described as exact).
- Got: 4 jobs for `test-linux2404-64/opt-mochitest-devtools-chrome-5` and `...-a11y-checks-5` without `--all-jobs`, 3 with it.
- Workaround: none; used the `--all-jobs` numbers.

## Manifest-level LeakSanitizer failure missing from `fx-tests task`

- Command: `fx-tests task SG5h3qzRTDi60GlyHb25Sg --profiles --messages --full-messages`
- Expected: the job's `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at ... | devtools/client/webconsole/test/browser/_browser_console.toml` lines listed (they are ERROR markers in the job's resource-usage profile), e.g. as a manifest-level failure row.
- Got: "2 failing" (two tests), nothing about the leak. The question was "did the skip reason (an asan shutdown leak for the manifest) reproduce in this job?"
- Workaround: loaded the resource-usage profile and ran `thread markers --search LeakSanitizer --list`.
