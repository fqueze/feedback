## `--bugs` prints nothing when there are no bugs
- Command: `fx-tests test devtools/server/tests/xpcshell/test_logpoint-03.js --bugs`
- Expected: a line saying that no bug names this test (or listing the bugs it found).
- Got: the same output as without `--bugs`, with no bugs section at all. I had to check `--json` (`annotatedBugs: []`) to see that the flag had run and found nothing.
- Workaround: `--json`, then a Bugzilla quicksearch.

## The failure message has no assertion line number
- Command: `fx-tests task DGJfOqsLS66aFU_vAV-6fA --messages`
- Question: which assertion in the test failed?
- Got: `false == true` only. The xpcshell log line normally carries `[<function> : <line>]`, as in the other tests' messages in the same output (e.g. `[test_mozillaonline_distribution_ignored : 72]`).
- Workaround: the marker stack in the profile (`Assert.prototype.ok` <- test file), plus the fact that the test has only one `Assert.ok`.
