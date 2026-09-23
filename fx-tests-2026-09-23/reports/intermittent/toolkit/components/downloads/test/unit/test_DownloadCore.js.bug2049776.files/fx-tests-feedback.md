## Issues row names a TEST-KNOWN-FAIL line as the failure

- Command: `fx-tests test toolkit/components/downloads/test/unit/test_DownloadCore.js`, and `fx-tests task QDGnHrqTQyiccW5G5G7-oA.0 --profiles`
- Expected: the FAIL issue labelled with its real first failure message, and a TIMEOUT test's message shown as the timeout.
- Got: `174x FAIL 31 == true`. `31 == true` is a `TEST-KNOWN-FAIL` / `TODO` line (an expected todo) that every run prints. `fx-tests task` also shows `31 == true` as the message of a TIMEOUT execution. The real messages were `File does not exist: …xpcshell-download-test.txt` (Windows, 105 jobs) and `Could not get extended attribute 'com.apple.metadata:kMDItemWhereFroms'` (macOS, 15 jobs), among others.
- Workaround: loop `fx-tests task <id> --messages` over all 159 task IDs and tally the messages with a script. That took several minutes and hundreds of lines.
- Question behind it: "which distinct failure messages make up this test's FAIL mode, with counts per platform and day". Skipping `TEST-KNOWN-FAIL`/`TODO` lines when choosing the "first failure" would answer it directly.

## fx-tests reads stdin

- Command: `while read t c; do fx-tests task $t --messages; done < list.txt`
- Expected: one call per line of the list.
- Got: the first `fx-tests` call consumed the rest of stdin, so only about 9 of the 159 tasks were processed, without any error.
- Workaround: `fx-tests ... < /dev/null`.
