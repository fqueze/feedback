## Question: what is this web-platform test's pass/fail rate per config, and since when?

- Command: `fx-tests test testing/web-platform/tests/html/canvas/element/manual/imagebitmap/createImageBitmap-resolves-in-task.any.js` (and `--history`)
- Expected: the rates per config and the per-day history, as for mochitest and xpcshell.
- Got: exit 2, "No test path in the xpcshell and mochitest 21-day data contains ...". There is no WPT data at all, although `fx-tests intermittent --bug` and `fx-tests task` do handle WPT jobs.
- Workaround: listed the config's jobs through the Treeherder `jobs/?job_type_name=...` API, then fetched `public/test_info/wptreport.json` from 211 tasks and scripted out the test's status and expected status. This took about 10 minutes and roughly 350 MB of downloads.
- What the output could have shown: harness status counts (OK/ERROR/TIMEOUT) per config per day, with expected vs unexpected. With that, the metadata flip on 2026-09-16 would have been visible directly.

## Question: which of this job's failures were unexpected?

- Command: `fx-tests task NoHQcph7QJ-0xmAZ72k2Bg --profiles`
- Expected: for WPT, only the tests whose status differed from their metadata expectation, or at least a mark on each saying which ones did.
- Got: "FAILED (16) — every test this job recorded a failure for", listing 16 ERROR tests. Only 1 of them was unexpected (`TEST-UNEXPECTED-ERROR`). The other 15 matched their `.ini` expectations. Nothing in the output tells them apart.
- Workaround: read the `expected` field in `wptreport.json`, which is present only when the result was unexpected, and grepped the log for `TEST-UNEXPECTED`.
