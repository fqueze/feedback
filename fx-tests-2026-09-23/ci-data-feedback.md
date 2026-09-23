# Firefox CI data gaps (from fx-tests feedback)

Things missing or wrong in the data CI jobs, test harnesses and artifacts record (not fx-tests bugs), from the same feedback files. Counts are the number of reports. Sources for each item: [ci-data-feedback-sources.md](ci-data-feedback-sources.md).

## 1. Android xpcshell harness failures carry no failure message, only INFO/WARNING lines (56)

remotexpcshelltests.py logs harness failures such as 'Failed to start process', 'Could not kill left-over process' or 'Failed wait for remote log' with log.info/warning and then a bare TEST-FAIL, so data shows 'Failure details not recorded'. The INFO line doesn't even name the test.

*Suggestion:* Log these harness errors as the TEST-UNEXPECTED-FAIL message for the affected test.

## 2. Resource-usage profile marks todo() known-fails as FAIL without expected status (23)

TestStatus markers for todo()/todo_is, a11y checks and requestFlakyTimeout (TEST-KNOWN-FAIL) say FAIL with no expected field, while the per-test profile says KNOWN-FAIL. Aggregates then record e.g. 'unable to perform a11y checks on hidden node' as the first failure of all 189 browser_settings.js failures.

*Suggestion:* Add the expected status to TestStatus markers (and log todos as TEST-KNOWN-FAIL everywhere).

## 3. Treeherder stores only TEST-UNEXPECTED-FAIL lines, dropping ERROR/TIMEOUT/PASS failures (19)

Treeherder failure lines keep TEST-UNEXPECTED-FAIL only, so Marionette TEST-UNEXPECTED-ERROR, WPT subtest TIMEOUT, unexpected passes, harness and build errors leave annotations with empty lines: 181 of 189 on one bug, 289 of 634 WPT jobs, 119 of 147 subtest timeouts.

*Suggestion:* Store all TEST-UNEXPECTED-* and harness error lines as failure lines.

## 4. Manifest-level LeakSanitizer/leakcheck failures missing from failure and errors data (18)

Leak failures attributed to the manifest (LSan shutdown leaks, leakcheck) appear in no test's failure data and not in the errors data; errorsummary.log reports group OK and the leaking test PASS. They are only visible via sheriff annotations or resource-usage profile markers.

*Suggestion:* Record manifest-level leak failures in errorsummary and the index, attributed to the manifest/group.

## 5. Jobs killed at max-run-time publish no results, their failures vanish (15)

Jobs hitting maxRunTime leave a partial profile and are dropped from the data: 36 of 98 killed jobs, 46 of 85 testfailed on one config. A near-perma failure shows 0% or 376/376 passes while 19-43 jobs were starred.

*Suggestion:* Upload the partial profile/errorsummary on max-run-time kill and index its results.

## 6. TSan and ASan jobs never upload per-test failure profiles (14)

linux2404-64-tsan/opt (and ASan opt) jobs upload no per-test profile for failing tests: 15 of 17 failing TSan jobs, and TSan is 103 of 135 failures of browser_searchTelemetry.js.

*Suggestion:* Enable per-test profile upload on sanitizer builds.

## 7. Msix process-launch failures reported as 45s per-test TIMEOUTs (13)

When launchProcess raises (WinError 5 CreateProcess access denied), the kill timer isn't cancelled, so tests that never started are logged TIMEOUT 45s later: 1149 tests ended in under 1s, 1152 per-test timeouts from one job-wide msix collapse, and the promised retry never ran.

*Suggestion:* Cancel the kill timer on launch failure and report a launch error, failing the job once.

## 8. Win32 per-test profile uploads fail with out-of-memory (9)

On windows11-32, per-test profile upload fails with NS_ERROR_OUT_OF_MEMORY (all 7 failures of one test), so no profile artifact exists for win32 failures, including OOM PROCESS-CRASH ones.

*Suggestion:* Stream or compress profiles on 32-bit to avoid OOM during upload.

## 9. Timeout records have an empty message; 'Test timed out' only in Subtest (9)

Mochitest harness timeouts are logged TEST-UNEXPECTED-FAIL with empty message and 'Test timed out.' in the subtest field, so the aggregate data says 'Failure details not recorded'.

*Suggestion:* Put 'Test timed out' in the message field of the timeout result.

## 10. Android jobs never upload per-test profiles (9)

The Android xpcshell and geckoview harnesses never pull per-test profiles from the device; the resource-usage profile has no Gecko threads, so timeouts leave only logcat evidence.

*Suggestion:* Pull per-test profiles from the device and upload them.

## 11. JUnit assumption violations and expected failures logged as TEST-FAIL (8)

The geckoview-junit harness logs AssumptionViolatedException/assumeThat skips and expected failures as TEST-FAIL markers without an Expected field.

*Suggestion:* Log assumption violations as SKIP and expected failures with expected status.

## 12. Multi-line console.error messages have an empty first line and are split (7)

'remote browser crashed' console.error starts with an empty line, so the profile marker message is empty and the text lands in separate output markers; the errors data indexes the first line only, making it unsearchable.

*Suggestion:* Log console.error as one marker with its full text.

## 13. Profile TestStatus records unexpected passes as plain PASS (6)

TEST-UNEXPECTED-PASS lines (and WPT unexpected subtest results) become PASS markers without expected status; the data instead records the 'profile uploaded in' line as the failure message.

*Suggestion:* Include expected status in TestStatus markers for unexpected passes.

## 14. errorsummary.log drops first-run failures when the harness retry passed (5)

errorsummary.log and annotation lines contain only the retry's lines; a first-run failure (e.g. a leaked window) that passed on rerun has no test_result line, and the job result doesn't show it.

*Suggestion:* Write first-run failures to errorsummary.log, marked as retried.

## 15. Failure profile uploaded under wrong name or overwritten after a failed upload (5)

When the first run's profile upload fails (OOM), the retry's profile takes the unsuffixed first-run name, breaking the -2 suffix convention; elsewhere the retry overwrites the first run's profile.

*Suggestion:* Reserve the name per execution regardless of upload success.

## 16. Marionette resource-usage profiles have no per-test markers (5)

Marionette, firefox-ui and telemetry-tests-client jobs' resource-usage profiles contain no test markers, so their per-test results can't be indexed; e.g. 21 of 25 starred jobs failed on test_new_tab_on_restore.py, visible only in logs.

*Suggestion:* Emit per-test TestStatus markers from the Marionette harness.

## 17. Per-test profile marker stack is the harness check, not the uncaught rejection's (4)

Uncaught-rejection failure markers carry the assertNoUncaughtRejections stack instead of the rejection's stack, which is only in the live log.

*Suggestion:* Attach the rejection's own stack to the marker.

## 18. xpcshell retry path logs no failure message or return code (4)

runxpcshelltests.py's retry path replaces the process's non-zero exit code with a generic message, so e.g. Windows xpcshell failures have no failure message.

*Suggestion:* Log the return code and failure reason on the retry path.

## 19. Crashes attributed to the wrong test (4)

Crash markers name the last test of the manifest (end-of-manifest GPU-process dumps) or the next test, and a crash in harness forceCC after test_end leaves the crashing test PASS.

*Suggestion:* Attribute crashes to the test that was running, or to the manifest if none.

## 20. Win32 xul.dll has zero debug_id, crashes unsymbolicatable (4)

The win32 xul.dll module has a zero debug_id, so 94 of 115 crashes show xul.dll+offset frames even when the log has a symbolicated stack.

*Suggestion:* Fix debug_id in win32 xul.dll symbol generation.

## 21. Some task runs never upload profile_resource-usage.json (3)

Some runs (infra-retried run e6fPXv0dTUWlpLrNpuNcMw.0, a one-day-old run .0, Firebase Test Lab flank jobs) produce no profile_resource-usage.json, so they are missing from the data.

## 22. test_end logs PASS despite failures after SimpleTest.finish or fatal conditions (3)

'Logged result after SimpleTest.finish()' failures and fatal-condition TEST-FAILs are followed by test_end status PASS, so the data records a pass.

*Suggestion:* Make test_end status reflect all failures logged during the test.

## 23. macOS minidump pid and uptime describe the dump writer, not the hung child (3)

For macOS out-of-process hang dumps, pid/process_uptime belong to the crash helper or parent writing the dump, not the dumped child.

*Suggestion:* Record the dumped process's pid and uptime.

## 24. Timeout cleanup exceptions skip test_end, so timeouts are unrecorded (3)

When timeout cleanup raises, the harness never logs test_end (traceback names no test), so e.g. test_crashreporter_crash.js's timed-out first run has no outcome and the test shows 100% pass.

*Suggestion:* Log test_end in a finally block.

## 25. Timeouts filed under SimpleTest/TestRunner.js instead of the test (3)

Before bug 2062615, a fourth-timeout victim's failures and the final hang before 'giving up' were logged against SimpleTest/TestRunner.js, recording the test as PASS.

## 26. Harness-written hang minidumps recorded as CRASH [Unknown] (3)

Minidumps the harness writes for force-killed processes are recorded as CRASH executions with [Unknown] signature and no crashing thread; one hang kill logs 9 CRASH [Unknown].

*Suggestion:* Label harness hang dumps distinctly from real crashes.

## 27. Minidump IDs of timeout-killed processes not recorded (3)

When a hung process is killed and its minidump uploaded, the outcome is TIMEOUT and no dump ID is recorded, e.g. for Windows hang dumps.

*Suggestion:* Record minidump IDs on TIMEOUT executions.

## 28. Minidump artifacts empty or truncated to 61 bytes (3)

Some parent-process minidump artifacts are 61-byte files missing the minidump header.

## 29. Minidumps uploaded but never processed into crash JSON (3)

A .dmp is uploaded but no processed .json exists, so the crash shows as CRASH [Unknown]; a parent-process dump was not uploaded at all.

## 30. Rerun-mode first run logs todos and real failures alike as TEST-FAIL (3)

In retry mode, the first run logs buffered todos as TEST-FAIL/TEST-TIMEOUT while retries log TEST-UNEXPECTED-*, making them indistinguishable in logs.

*Suggestion:* Log first-run results with the same expected/unexpected statuses as retries.

## 31. Tests that stop the profiler or set MOZ_PROFILER_STARTUP never get per-test profiles (3)

When a test (or an earlier test in browser.toml) stops the profiler, or the manifest sets MOZ_PROFILER_STARTUP, no per-test profile is uploaded for later failures.

*Suggestion:* Restart the profiler after each test.

## 32. xpcshell head.js uploads no profile for cleanup-function-only failures (3)

Failures in registerCleanupFunction don't set _passed=false, so head.js uploads no failure profile.

*Suggestion:* Set _passed=false on cleanup failures.

## 33. Leak messages stamped with creation time, not log time (2)

runtests.py stamps shutdown leak messages with the leaked object's creation time, ordering them before the causing exception.

*Suggestion:* Stamp with log time.

## 34. Leak check runs after the next test's profile capture (2)

The failing test gets no per-test profile for its shutdown leak.

## 35. Leakcheck 'missing output line for total leaks' printed in every debug run (2)

This TEST-UNEXPECTED-FAIL appears in green runs too and never turns the job orange.

*Suggestion:* Fix the leakcheck to stop printing it.

## 36. Android lowmemorykiller kills reported as PASS (2)

Tests SIGKILLed mid-run are reported PASS; a force-killed rerun counted as pass.

*Suggestion:* Treat killed runs as failures.

## 37. Resource-usage profile lacks passing tests' TEST-PASS lines (2)

Passing tests' lines are buffered and absent.

## 38. Resource profile lacks helper-process output (2)

Node test server stderr (EADDRINUSE) and ssltunnel stdout appear only in the live log.

## 39. Broken workers fail every job (2)

17 macOS 10.15 workers crash in every job; xattr failure is 100% on 5 of 172 macOS workers.

*Suggestion:* Quarantine workers failing consistently.

## 40. ASan/TSan jobs write no minidumps (2)

Windows ASan job has crash reporter off, so content crash shows as TIMEOUT.

## 41. Harness failed to write a minidump (2)

Harness logged 'unable to dump minidump file for pid'; crash helper rendez-vous failed.

## 42. Opt crash dumps lack MozCrashReason (2)

Opt crash dumps lack the MozCrashReason annotation.

## 43. GTEST_SKIP tests logged as TEST-FAIL markers (2)

Skipped gtests (GTEST_SKIP) are recorded as TEST-FAIL markers in the resource-usage profile.

*Suggestion:* Log GTEST_SKIP as SKIP.

## 44. Per-test profiles corrupted by a shared timeout-dump path (1)

In test_BHRObserver.js failures, 3 of 4 per-test profiles were corrupted because the child and parent processes wrote to the same timeout-dump path.

*Suggestion:* Use per-process dump paths.

## 45. Same task label runs different manifest sets; retriggers copy the failing set (1)

On autoland, test-macosx1015-64-qr/opt-crashtest tasks run different MOZHARNESS_TEST_PATHS sets; backfills copy the failing set, so an order dependency looked perma (216/216 fails with one set, 0/36 with others).

*Suggestion:* Record the manifest set per task in the index.

## 46. os-integration alpha-pool runs mixed into regular config rates (1)

21 of 22 test_backends.html failing jobs ran on gecko-t/win11-64-24h2-gpu-alpha from os-integration cron groups, but are counted under the regular win11 config.

*Suggestion:* Record worker pool and flag os-integration runs.

## 47. Shutdown leak logged twice with different text (1)

Each shutdown leak is printed by browser-test.js and again by runtests.py's leak parser with different text, so one failure mode looks like two (browser_starting_autoscroll_in_about_content.js).

*Suggestion:* Log the leak once.

## 48. xpcshell harness fails to parse glued CHILD-TEST-COMPLETED JSON (1)

On Windows, CHILD-TEST-COMPLETED was glued to another JSON line; the harness failed to parse it and failed the test with no failure line (12 failures).

*Suggestion:* Parse robustly or log a failure message.

## 49. system-info.log boot time unreliable (1)

The Boot time line in system-info.log is quantized to 4s and sometimes logged late, while test_restyles.html's crash only happens within 100s of boot.

## 50. macOS resource-usage profile has no CPU tracks (1)

The resource-usage profile of a macOS task (XvztFRm3TVmLPZsJ_7yqAw) had no CPU counters or tracks, so machine saturation can't be checked.

*Suggestion:* Record CPU counters on macOS.

## 51. Android xpcshell timeouts lack full-log block; parent pid logged as 0 (1)

Android xpcshell live logs have no full-log block for timeouts (only the force-kill TEST-ERROR), and 'Launched Test App' logs parent pid 0, forcing 59 logcat downloads (bug 2043133).

## 52. JUnit ErrorCollector failure line carries only the last error (1)

With several ErrorCollector errors in TextInputDelegateTest.kt, only the last reaches the failure line, not the real first one.

*Suggestion:* Report all collected errors.

## 53. GPU child process dump logged as 'Process type: main' (1)

The harness log prints 'Process type: main' for a GPU helper process minidump.

## 54. Helper-window assertion logged as unknown test url, test recorded PASS (1)

test_autofill_from_bfcache.html shows 1 fail in 9,797 runs while 34 were starred: the helper window's assertion is logged as 'unknown test url' and the test recorded PASS.

## 55. Snap Selenium nightly job silently unscheduled (1)

The snap Selenium QA job (bug 2009873, test_pdf_navigation) wasn't scheduled from 2026-08-06 to 09-11 without notice, and its results are not indexed.
