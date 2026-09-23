## `crash` omits the .extra annotations that explain an OOM crash

- Command: `fx-tests crash FC2dBXcZTqefvlXpMT-yqQ.0 70fa6428-4afe-4a6d-8722-53eee53a2d8f` (and `--raw`)
- Expected: `MozCrashReason`, `OOMAllocationSize`, `TotalVirtualMemory`, `AvailableVirtualMemory`, `AvailablePhysicalMemory`, `JSOutOfMemory` — for a 32-bit OOM these say whether the address space or the machine ran out.
- Got: signature, `EXCEPTION_BREAKPOINT`, stack only; `--raw` is the stackwalk JSON without the `.extra`. Two crashes showed only `xul.dll + 0x…` with no reason at all.
- Workaround: `curl https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/<task>/runs/0/artifacts/public/test_info/<dumpId>.extra`.

## `task --profiles` says no per-test profile was named, not why

- Command: `fx-tests task A2Ea17O0SH6c2-HtIO175Q.0 --profiles`
- Expected: when the harness tried and failed to capture the profile, say so.
- Got: "No failing test named a per-test profile in this job." The job's log has a `FAIL failed to upload profile: [Exception... "Component is not available" NS_ERROR_NOT_AVAILABLE]` for the test, found only by reading the resource-usage profile.

## `test --bugs` prints nothing when no bug summary names the test

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_topsites.js --bugs`
- Expected: an explicit "no bug names this test" line, ideally with bugs the test's failures were starred on (bug 2067919, the OOM tracking bug, has 115 win32 annotations).
- Got: the usual output with no Bugs section, which reads the same as the flag being ignored.
