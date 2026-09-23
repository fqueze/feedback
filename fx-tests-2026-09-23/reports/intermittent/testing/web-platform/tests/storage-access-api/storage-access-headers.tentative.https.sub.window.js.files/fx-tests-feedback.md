# fx-tests feedback (storage-access-headers.tentative.https.sub.window.js, WPT)

## Question: "what did this job's starred failure look like?" (subtest-level unexpected result)

- Command: `fx-tests task KoaAkhY-SrqFjmL6UU51-Q --profiles`
- Expected: the job was starred on bug 1996306 for
  `TEST-UNEXPECTED-PASS | /storage-access-api/storage-access-headers.tentative.https.sub.window.html | Sec-Fetch-Storage-Access is `active` after a valid retry ... - expected FAIL`,
  so I expected that test in the "FAILED" list.
- Got: "FAILED (9) — every test this job recorded a failure for" listed 9 other tests (all with
  expected ERROR/TIMEOUT harness statuses) and not the one test the job was starred for. The
  unexpected subtest result was invisible.
- Workaround: downloaded `public/test_info/wpt_errorsummary.log` and `wptreport.json` and parsed them.
- What could have shown it: list unexpected subtest results (status/expected per subtest), and
  separate expected from unexpected failures.

## Question: "how often does this WPT subtest pass vs fail on one config, and what differs between the two?"

- Command: `fx-tests test testing/web-platform/tests/storage-access-api/storage-access-headers.tentative.https.sub.window.js` (also `--history`)
- Expected: per-config rates, like for mochitest/xpcshell.
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...".
- Workaround: `treeherder-cli --similar-history <jobId>` for five chunks, resolved 317 task ids via
  the Treeherder jobs API, stream-grepped each `live_backing.log`, and parsed `wptreport.json`
  for runs where the expected result is not printed. Costly: about 20 minutes and a few hundred MB of logs.
- What could have shown it: WPT support in `fx-tests test`, with per-subtest outcomes (including
  expected ones, which the mach log omits for green jobs) per run, so pass/fail runs can be
  compared with the outcomes of the tests that ran before them in the same browser.
