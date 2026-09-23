## `--bugs` prints nothing when no bug names the test

Command: `fx-tests test browser/components/urlbar/tests/browser-engagementTelemetry/browser_glean_telemetry_bounce.js --bugs`
Expected: a "Bugs" section, or an explicit "no bug names this test" line.
Got: the same output as without `--bugs`; I had to check `--json` (`annotatedBugs: []`) to see that the flag had
been honoured, then query Bugzilla, which found bugs filed under the test's previous path
(`engagementTelemetry/browser/…`, renamed in bug 2028423).
Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=browser_glean_telemetry_bounce`.
What the output could show: "none" plus bugs matching the file name under an old path.
