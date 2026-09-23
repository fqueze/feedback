## Question: "which failure modes are behind one bug's annotations, and which belong to another bug" (bug 915212)

- Command: `fx-tests intermittent --bug 915212 --since 30 --limit 0` (and `--json`)
- Expected: occurrences grouped by the discriminating log line that precedes the starred one. For this bug the starred line is always the generic `TEST-UNEXPECTED-FAIL | runtests.py | Timed out while waiting for server startup.`, and the cause is in the server's stderr a few lines earlier (`!!! could not start server on port N: TypeError: ... this._host is undefined` vs `... NS_ERROR_SOCKET_ADDRESS_IN_USE`).
- Got: every occurrence reports only the starred line (`lines` in JSON), so all 55 look identical. It could not show that 32 of 55 are the failure another bug (1976612) names in its summary, i.e. mis-stars.
- Workaround: downloaded all 175 `live_backing.log` files of the 120-day window and grepped them (`HOST_IP`, `UserPorts`, `could not start server`, the shutdown-request error). About 45 MB and several minutes.
- What would have answered it: a `--context <n>` (or `--log-grep <re>`) on `intermittent --bug` that prints/greps the log lines before the starred one, plus a "same text as bug N's summary" hint when the context matches another bug's summary.

## Question: "how many jobs ran on this config" for a harness-level failure (bug 915212)

- The failure is `runtests.py`, not a test, so `fx-tests test` has no denominator. I used a test in the first manifest of the affected jobs (`fx-tests test dom/media/autoplay/test/mochitest/test_autoplay_policy.html --coverage`) as a proxy, and the coverage table was cut (only 3 of the 19 android configs listed, no `--limit 0` hint shown).
- What would have answered it: per-config job counts (`fx-tests manifests --job <config>` or a `jobs` count) independent of any one test.
