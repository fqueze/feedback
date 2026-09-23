## Manifest-level failures are invisible in `try` and `task`

- Command: `fx-tests task SG5h3qzRTDi60GlyHb25Sg --profiles` (and `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids`)
- Question: did removing the manifest-level `skip-if = ["asan"]` (Bug 1910261, LeakSanitizer leak at shutdown) bring its failure back?
- Expected: the job's `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at ... | devtools/client/webconsole/test/browser/_browser_console.toml` lines listed under FAILED, since they turn the job orange.
- Got: "2 failing", both tests; no mention of the 9 LeakSanitizer failures attributed to the manifest. `fx-tests try --test <path> --all-jobs` says 3/3 passed on that config, which is true for the test but hides that the reason for the skip still reproduces.
- Workaround: loaded the resource-usage profile and ran `thread markers --search LeakSanitizer --list`.

## `try --test ... --task-ids` prints no task IDs

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --test devtools/client/webconsole/test/browser/browser_console_and_breakpoints.js --all-jobs --task-ids` (also `--json`)
- Question: which tasks ran this test and passed, so I can read their resource-usage profiles?
- Expected: the task IDs behind each config row.
- Got: counts only, in text and in JSON. Had to recover the Linux asan task IDs from the unrelated failures listed by the default `try` view; the Windows asan ones (all green) I could not get.
