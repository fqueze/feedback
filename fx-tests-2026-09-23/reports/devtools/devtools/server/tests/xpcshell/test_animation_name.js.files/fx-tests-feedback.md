## `fx-tests test <path> --bugs` printed no bug section at all

- Command: `fx-tests test devtools/server/tests/xpcshell/test_animation_name.js --bugs`
- Expected: a "Bugs" section, saying "no bug names this test" when none does, and ideally the bugs sheriffs starred this test's failing jobs on (here all 6 msix jobs are bug 1991833, "High frequency win-msix PermissionError: [WinError 5] Access is denied", which names no test).
- Got: the same output as without `--bugs`, no section, so "no bug" and "flag ignored" look identical.
- Workaround: searched Bugzilla by hand (`short_desc=msix`), then `fx-tests intermittent --bug 1991833`, whose occurrence list included one of this test's task IDs.

## `fx-tests task` does not flag a job where most tests failed at once

- Command: `fx-tests task JOcVulCfRyynVd-aExDW1g --profiles`
- Got: "1061 failing ... 1060 TIMEOUT", then 1061 per-test entries each saying "Timed out and was force-killed".
- Could have shown: that 1060 tests started within ~4 s and all ended ~45 s later (none of them actually ran), i.e. a job-wide infra meltdown, plus the job's exception (`PermissionError: [WinError 5] Access is denied` from `launchProcess`), instead of per-test timeouts.
