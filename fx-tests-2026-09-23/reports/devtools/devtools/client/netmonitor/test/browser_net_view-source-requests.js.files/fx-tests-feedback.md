## `fx-tests test <path> --bugs` printed no bug, while one names the test (browser_net_view-source-requests.js)
- Command: `fx-tests test devtools/client/netmonitor/test/browser_net_view-source-requests.js --bugs`
- Expected: bug 2032311 ("Intermittent TV devtools/client/netmonitor/test/browser_net_view-source-requests.js | Test timed out", NEW), or an explicit "no bug found" line.
- Got: the same output as without `--bugs`: no Bugs section, no "none" line.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=browser_net_view-source-requests`.

## Question: did the try push run the config the removed skip-if applied to?
- Commands: `fx-tests try 2888bcab0070 --test <path> --all-jobs` (7 configs, 21 passes) and `fx-tests test <path> --coverage --limit 0` (skipped only on `test-linux2404-64/debug-mochitest-devtools-chrome-http3`).
- Neither says that the push never scheduled any `-http3` job, so "passed everywhere" could be read as "the skip can go". I confirmed it with `treeherder-cli <rev> --json` and a script over job_type_name.
- What could show it: `try --test` listing central's skipped configs for that test with "not scheduled on this push".
