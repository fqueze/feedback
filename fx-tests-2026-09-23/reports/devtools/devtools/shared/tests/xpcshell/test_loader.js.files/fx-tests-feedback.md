## `--bugs` prints nothing when no bug matches
- Command: `fx-tests test devtools/shared/tests/xpcshell/test_loader.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test" line.
- Got: the same output as without `--bugs`; no bug section and no statement that none was found, so "no bug" and "flag ignored" look identical.
- Workaround: Bugzilla REST `summary=test_loader.js` search.

## Question: "was this failure part of a job-wide breakdown?"
- Command: `fx-tests task ZtblArCLSx6O6KzfEkL1YQ --profiles --limit 0`
- The header says "750 TIMEOUT" out of 920 tests, which hints at it, but the output then lists 755 tests one by one (3000+ lines) before the one I cared about.
- What would have answered it: a one-line verdict such as "750 of 920 tests timed out, all started within 1m3s-1m8s; job-wide failure", and grouping identical failures instead of listing each.
