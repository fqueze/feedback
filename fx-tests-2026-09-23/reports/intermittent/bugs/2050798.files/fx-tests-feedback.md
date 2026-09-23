## Question: which test emitted the bug's own message in each annotated job?

- Command: `fx-tests intermittent --bug 2050798 --since 60` (and `--json`)
- Expected: for a bug whose summary is a crash/assertion message rather than a test path, the test(s) whose output contains that message in each annotated job (here the `INFO crashed process | ... | MOZ_ASSERT(false) (Late preference writes should be avoided.) ... | <test>` lines).
- Got: "Tests named, per annotated job" lists the tests of the job's TEST-UNEXPECTED lines (test_new_profile.js 81x, test_backgroundupdate_exitcodes.js 45x, ...), none of which emits the assertion; 51 occurrences have no lines at all. The tests that actually crash with the bug's assertion (test_IPProtectionStates.js, test_IPPL10nHelper.js, ...) appear nowhere, because their crash is quiet (retried, passed) and never a TEST-UNEXPECTED line.
- Workaround: downloaded all 233 live_backing.log files and grepped `crashed process` lines matching the bug summary. Several minutes and ~1 GB of transfer.
- What the output could show: per bug, the tests whose crash reason / log line matches the bug summary, next to the tests whose failure actually turned the job orange, so a mis-starred magnet bug is visible as such.

## `fx-tests task --profiles` omits the crash reason of CRASH entries

- Command: `fx-tests task JtaXRe_0R4qfIjW0hq4-5A --profiles`
- Expected: the MozCrashReason for each CRASH (it is in the log's `crashed process` line).
- Got: `CRASH ... [Unknown]` for test_IPPL10nHelper.js, whose log line says `MOZ_ASSERT(false) (Late preference writes should be avoided.) [[Unknown]]`. The signature is unresolved, the reason was there.
- Workaround: grepped the log.
