## Question: what did these runs fail on first?

- Command: `fx-tests test devtools/client/aboutdebugging/test/browser/browser_aboutdebugging_addons_debug_storage.js`
- Expected: the 29 debug failures counted under the message they failed on first, `The indexedDB database for the extension is visible`, with the leaks as fallout.
- Got: `Issues (first failure per run)` puts them in 2 rows of their own: `18x leaked 1 window(s) until shutdown [url = about:debugging]` and `11x leaked 1 docShell(s) until shutdown`. Those messages are logged at shutdown, but `runtests.py` gives each one the creation time of the leaked window or docShell (`"time": error.get("time")`). So a sort by time puts them before the assertion, and the parent inserted a separate row for a mode that is the IndexedDB failure again.
- Workaround: `fx-tests task <id> --messages` on each of the 29 task IDs. Every one listed the assertion, `Test timed out` and the leaks.
- Could show: skip `subtest: "Shutdown"` leak messages when a run has another failure, or mark them as logged at shutdown.

## Question: which bugs name this test?

- Command: `fx-tests test <path> --bugs`
- Expected: a Bugs section, or a line saying none was found.
- Got: the usual output with no Bugs section and no "none found" line, so I could not tell "no bugs" from "flag ignored". Bugzilla has Bug 1767695, the test's tracking bug (RESOLVED INCOMPLETE).
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=<test file name>`.
