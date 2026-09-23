## Report 2071976 (wpt-sync bug, web-platform-tests jobs)

- Question: "is this wpt test failing, on which configs, since when, and is it fixed now?"
  Command: `fx-tests test testing/web-platform/tests/webcodecs/video-encoder-hbd.https.any.js`
  Expected: rates/history for a wpt test (sheriffs star wpt failures on bugs that `fx-tests intermittent --bug` does list).
  Got: "No test path in the xpcshell and mochitest 21-day data contains ...". No wpt coverage at all.
  Workaround: `treeherder-cli --similar-history <job id>` for the job's pass/fail history, plus a script over each task's `public/test_info/wpt_errorsummary.log` (filtering out `known_intermittent`) to get the unexpected subtests.

- Question: "what failed unexpectedly in this wpt job?"
  Command: `fx-tests task AW8Ct8TRSBmwjuwqPe66Zw --profiles`
  Expected: the unexpected subtest results. The job is orange because of ~200 subtest FAIL/PASS mismatches in `video-encoder-hbd`, `video-encoder-rescaling`, etc.
  Got: "95 tests ... 90 OK, 5 ERROR", listing only tests whose top-level status was ERROR (mse-for-webcodecs, image-decoder), some of which were expected. It did not mention `video-encoder-hbd` at all. For wpt, a test-level OK with unexpected subtests is the usual failure shape, so this output points at the wrong tests.
  Workaround: the errorsummary script above, comparing `status` with `expected` and `known_intermittent`.
  Correction to the entry above: none of those 5 ERROR tests appear in the job's `wpt_errorsummary.log`, so all 5 ERRORs were expected results. The list named only expected outcomes and missed every unexpected one.
