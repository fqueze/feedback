## `test --profiles --task-ids --issue <n>`: the Profiles block ignores `--issue`

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_contentscript_triggeringPrincipal.js --profiles --task-ids --issue 9 --limit 0`
- Expected: profile URLs for the 59 jobs of issue 9 (`CRASH @ NS_DispatchToMainThread`).
- Got: the Task IDs block was filtered to issue 9, but the "Profiles" block listed resource-usage URLs for jobs of other failure modes (Android and linux-artifact timeouts), and none of the issue-9 jobs I checked.
- Workaround: `fx-tests task <taskId> --profiles` per job.

## Question: "which tests are behind this crash signature?"

- Command: `fx-tests crashes --signature NS_DispatchToMainThread --limit 0`
- Got: `558 crashes, 12 tests`, but not which 12 tests. The list is only in `--json` (`rows[].tests`).
- Could show: the tests and their counts under each signature, at least with `--limit`.

## Question: "is there a per-test profile for this crash?"

- Command: `fx-tests task VmPHw38kTMqj4FMHuBDI7g --profiles`
- Got: the CRASH row with no profile line, with nothing saying why. A crash uploads no per-test profile, but the job's resource-usage profile replays the whole test log, the child's `###!!! ASSERTION` and its stack included.
- Could show: "no per-test profile (crash); the test's log is replayed in the resource-usage profile", with that URL.
