## `fx-tests task` does not flag a job-wide breakdown

- Command: `fx-tests task ZtblArCLSx6O6KzfEkL1YQ --profiles`
- Question: "did this test fail on its own, or did the whole job fall over?"
- Expected: a verdict line when most of a job fails at once, e.g. "755 of 920 tests failed, 750 TIMEOUTs all starting within 3 s at t=66 s: job-wide", plus the harness's own traceback (here `PermissionError: [WinError 5] Access is denied` from `CreateProcess`) and the bug sheriffs star it on (1991833).
- Got: "1061 failing" and a list of per-test TIMEOUT rows. Working out that everything broke at one instant took a `profiler-cli thread markers --search name:test --list --limit 0 --json` dump and a script bucketing test starts by status over time.

## `fx-tests test --bugs` prints nothing when no bug names the test

- Command: `fx-tests test devtools/shared/tests/xpcshell/test_sprintfjs.js --bugs`
- Expected: a "Bugs: none name this test" line, ideally with the bugs the failing jobs were starred on (here 1991833, which names no test).
- Got: the normal output with no Bugs section at all, which reads the same as the flag being ignored. I found 1991833 through a Bugzilla summary search.

## Which manifests a job ran

- Question: "why did this test stop failing when the job-wide failure it was caught in still happens?"
- Answer: autoland msix xpcshell jobs run a per-push manifest selection (`MOZHARNESS_TEST_PATHS` in the task payload). I read it with curl on the Taskcluster task definition. `fx-tests task` could print the selected manifests.
