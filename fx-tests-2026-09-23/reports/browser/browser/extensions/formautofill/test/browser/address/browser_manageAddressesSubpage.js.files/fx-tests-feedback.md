# fx-tests feedback

## `test` Issues names a TEST-KNOWN-FAIL as the failure

- Command: `fx-tests test browser/extensions/formautofill/test/browser/address/browser_manageAddressesSubpage.js`
- Expected: "Issues (first failure per run)" to name the message that made the run fail.
- Got: `58x FAIL changed preference: identity.fxaccounts.account.device.name`. In the profiles
  (tasks WygSRaKpQU6K6kfW7Hzo7g, Fk1lLyoURqazraG5qpDtsQ) that result is a `TEST-KNOWN-FAIL`
  (browser-test.js records `changed preference:` as todo unless `comparePrefs` is set). The
  `TEST-UNEXPECTED-FAIL` is the next one: `This test exceeded the timeout threshold ... Test ran
  for 53s, limit was 45s`. `fx-tests task --messages` lists both as if equal.
- Cost: the first half-hour went into the fxaccounts device-name pref, which is not what fails.
- Workaround: read the test's `Test` markers in the per-test profile, where the log level
  (`TEST-KNOWN-FAIL` vs `TEST-UNEXPECTED-FAIL`) is visible.
- Could show: skip known-fail/todo results when picking the "first failure", or print the log
  level next to each message.

## Question: "how busy was the machine during this test?"

- Command: `profiler-cli thread markers --session <ru> --search "name:CPU Use" --list --limit 0 --json | jq ...`
  after `zoom push` on the test's `test` marker in the resource-usage profile.
- Needed a jq script to average `cpuPercent` over the test's 619 `CPU Use` markers; the default
  output gives per-marker rows only. `fx-tests task` could print the machine's mean/max CPU over
  each failing test's interval.

## Question: "where inside a passing run did the time go?"

- Needed: for passing executions (no per-test profile), when each of the test's three page loads
  happened, to see which loads were slow. The resource-usage profile has `INFO` markers
  (`Entering test ...`) only for the failing execution.
- Workaround: downloaded `public/logs/live_backing.log` of 7 jobs and ran a Python script over
  the `TEST-START` lines and a per-window console message (`I/SlowChromeEvent ...`), which gives
  one timestamp per page load.
- Could show: `fx-tests task <id> --passed` could include each passing test's log-line
  timestamps (or its `Entering test`/`Leaving test` subtest boundaries when present).
