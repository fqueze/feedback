## A TEST-KNOWN-FAIL message is reported as the failure message

- Command: `fx-tests test toolkit/components/downloads/test/unit/test_DownloadCore.js` and `fx-tests task QDGnHrqTQyiccW5G5G7-oA --profiles`
- Expected: the Issues row and the per-test line to show the message that failed the run: `Test timed out`, `File does not exist: C:\...\xpcshell-download-test.txt`, or `Unexpected exception NotFoundError: Could not get extended attribute ...`.
- Got: `174x FAIL 31 == true` in Issues, and `TIMEOUT — 31 == true` in `task`. `31 == true` is a `TEST-KNOWN-FAIL` (todo) from `todo_check_true` that every run logs, passing ones included. So the Issues row lumps two unrelated failure modes (144 Windows jobs that stopped on 2026-09-11, and 15 macOS jobs that fail on the retry too and are still happening) under one todo message. The TIMEOUT runs show it too.
- Workaround: `fx-tests task <id> --messages` on a few tasks from each OS, dropping the `31 == true` / `62 == true` lines.

## The revision printed is the hg hash, which I could not use

- Command: `fx-tests task QDGnHrqTQyiccW5G5G7-oA` prints `autoland ad9dc82bc357`.
- Expected: a hash I can read the test source at.
- Got: `curl https://hg.mozilla.org/integration/autoland/raw-file/ad9dc82bc357/...` answered HTTP 406 (after a redirect to hg-edge), and the hash does not exist in the git checkout.
- Workaround: `curl https://lando.moz.tools/api/hg2git/firefox/<hg hash>` gave the git hash (41f616292e9f), then raw.githubusercontent.com/mozilla-firefox/firefox/<git hash>/... worked. Printing the git hash alongside the hg one would save this step.
