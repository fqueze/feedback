# Firefox profile data gaps (from profiler-cli feedback)

Things missing or wrong in the profiles Firefox records (not profiler-cli bugs), from the same feedback files. Counts are the number of reports. Resource-usage profile data issues are in [resource-usage-profile-feedback.md](resource-usage-profile-feedback.md). Sources for each item: [firefox-profile-data-feedback-sources.md](firefox-profile-data-feedback-sources.md).

## 1. IPC and network payload times use a different time base than marker times (62)

IPC (`startTime`, `sendStartTime`, `recvEndTime`, `endTime`) and network (`startTime`, `requestStart`, `responseStart`…) payload fields aren't rebased to profile zero. They're offset by 0.3–40 ms (usually the uptime/profile start) from the marker's own start, which led to wrong latency and causality conclusions.

*Suggestion:* Rebase these fields to profile time when the profile is written, and give them schema entries.

## 2. Negative IPC durations from cross-process clock skew; IPCIn starts at sender time (23)

IPCIn/IPCOut markers often have negative durations, and replies appear before requests (especially on Windows), because the marker start mixes sender and receiver clocks. IPCIn starts at the sender's send time rather than when the receiver handled the message.

*Suggestion:* Record receiver handling time on IPCIn, and correct or record the per-process clock offset.

## 3. C++ warning markers flagged as having a stack, but the stack is `unknown!null` (19)

Log-derived "C++ warning"/cppDebug markers (mostly in resource-usage profiles) have the stack flag set, but the stack is a single `unknown!null` frame. There's no call site to see.

*Suggestion:* Don't set the stack flag without a real stack, or capture the real one.

## 4. Runnable/AddTask flow ids are reused addresses; no reliable link to the dispatch (19)

Task/flow ids are recycled pointers, so a Runnable can't be reliably paired with its `TaskController::AddTask`. Runnables also lack a queued-at time, and timers or runnables dispatched from unprofiled threads leave no AddTask at all. "Who dispatched this, and when" goes unanswered.

*Suggestion:* Use unique flow ids, and record the queue time (and timer-armed time) on Runnable markers.

## 5. DummyEvent Runnable markers bloat xpcshell profiles until they can't be loaded (16)

On macOS xpcshell especially, the idle loop records millions of `DummyEvent` Runnable + `TaskController::AddTask` markers (~40k/s). Per-test profiles reach 870+ MB uncompressed, too large to load, and drown other runnables.

*Suggestion:* Stop recording markers for DummyEvent (or fix the busy loop).

## 6. Harness/log markers lack a test name field (12)

Mochitest-plain Log/TEST-* markers, and harness INFO markers ("will retry", launch_application, return code), carry no test file field. They can't be tied to a test, and unnamed add_task functions get empty "Entering test" names.

*Suggestion:* Add the current test path as a field on all harness-emitted markers.

## 7. Harness markers duplicated, mistimed or miscategorized (12)

xpcshell records each log line twice. CONSOLE_MESSAGE uses harness log time. Resource-usage test markers are in category Other, carry a lagging test name, and lack task markers. Group markers are named `test`.

## 8. Live child processes missing from profiles (10)

Content processes that were alive and involved (IPC peers, the process hosting the test, hung processes) are missing from the profile. Gathering timed out, the profile was discarded for size, or no threads were profiled, and this is noted only in profileGatheringLog, if anywhere.

*Suggestion:* Include partial data for timed-out processes, and surface gathering failures in the profile meta.

## 9. DocAccessible::ContentRemovedNode marker flood overflows the buffer (10)

About 7M a11y `DocAccessible::ContentRemovedNode` markers in a few seconds fill the parent main thread buffer. Everything but the last few seconds is lost, including the failing subtest, and the profile doesn't indicate the wrap.

*Suggestion:* Throttle or aggregate these markers.

## 10. Marker schemas missing or without a table label (10)

RefreshDriverTick "waiting for paint", CompositorScreenshot(WindowDestroyed), IO/Memory/NetIO/CPU Use and Timeupdate markers have no schema or no tableLabel, so key fields like innerWindowID or write_bytes are raw or hidden.

## 11. Mochitest INFO Log markers show `(empty)` Level/Message (9)

Log markers from `info()` have a broken schema format, so Level/Message render as `(empty)` while the raw value holds the text (bug 2052468 related).

## 12. macOS sampling far sparser than the configured interval (9)

macOS CI threads are sampled every 38–63 ms despite a 10 ms interval, so short janks have zero samples and busy versus idle can't be judged. The effective rate isn't recorded.

## 13. CompositorScreenshot data gaps (7)

Screenshots are ~350 px wide, lack screen position, and don't mark closed windows. Some are all black or hold an unresolved string index, none are recorded despite the feature being on, or there's no transaction id linking them to main-thread paints.

## 14. Markers lack window/page context (innerWindowID, browsing context) (6)

Reflow for SVG images, NotifyObservers, PBrowser/PWindowGlobal IPC and JSActor messages carry no innerWindowID or browsing context, so they can't be tied to a tab or window.

## 15. Sample categories and CPU deltas misleading (6)

Blocked threads are categorized as running (no Idle on macOS), duplicated samples aren't flagged, and Windows threadCPUDelta exceeds elapsed time or is null.

## 16. Payload values formatted with thousands separators or packed (6)

Ids, pids and WindowProc lParam are printed with thousands separators and aren't decoded (e.g. mouse x/y), and GCMajor puts reason and zones inside a JSON string.

## 17. DOMEvent markers don't identify the target (5)

DOMEvent markers with target "window"/"document" don't say which window or document (no innerWindowID/URL), record only the retargeted target, and have an empty eventType for internal events.

## 18. Profile meta incomplete (5)

Platform shows Unknown, and product name, CPU core count and counter units are missing; the thread name is "(, mach)" on Android.

## 19. IPC request/reply markers can't be paired (4)

IPC markers lack routing/actor ids, seqnos look reused, and JSActor query/reply are two unpaired instants with no wait duration.

## 20. Preference Read markers flood the main thread and carry huge values (4)

Millions of Preference Read markers swamp searches, and each network.proxy.autoconfig_url read carries the whole PAC script.

## 21. Uncaught rejection / JS error markers lack the origin stack (4)

TestStatus markers for uncaught rejections have only the harness stack, and JavaScript error markers have one frame and no async stack.

## 22. Network markers wrong for proxied HTTPS (3)

Through an HTTPS proxy tunnel, secureConnectionStart is after connectEnd (negative TLS), and responseStart is the CONNECT reply, so TTFB is wrong.

## 23. Network markers without an end or timings (3)

Requests with STATUS_START and no end, canceled requests with empty timings, or end times past the profile end.

## 24. RefreshDriverTick stack is the previous tick's cause stack (3)

The stack was captured tens of ms earlier, inside a previous tick, and isn't labelled as a cause stack.

## 25. Thread lifetimes wrong (3)

Threads of exited processes have no end time, and createdAt is later than the thread's first markers.

## 26. Process startTime identical for all processes (2)

Every process gets the same startTime, even ones launched minutes later.

## 27. Buffer too small for long per-test profiles (2)

Early main-thread samples and markers are lost when the buffer wraps.

## 28. Marker stacks truncated or start with profiler frames (2)

CRASH marker stacks are cut at 20 frames, and some stacks start with `AddMarkerToBuffer` frames.

## 29. Other single reports (10)

Unprofiled thread IPCOut filed on GeckoMain; macOS Awake spans idle; ModuleEvaluation has no module URL; failed RDP requests leave no marker; ObserveActivity lacks subtype; DOMWindow of crashed process is instant; garbled JIT stacks; DOM Worker threads unnamed; hang-at-shutdown markers lack process type.

