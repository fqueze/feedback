## Question: which tests are behind one crash signature?

- Command: `fx-tests crashes --signature NS_DispatchToMainThread`
- Expected: the list of tests (with counts) contributing to the 558 crashes.
- Got: one row, `558  12  0  @ NS_DispatchToMainThread`; the 12 test names only appear with `--json` (`rows[].tests`).
- Workaround: `--json`. The text output could list the tests under the row when a single signature is selected.

## Question: does one crash signature hide several different crashes?

- Command: `fx-tests crashes --signature NS_DispatchToMainThread`, then `fx-tests crash <task> <dump>` on one task per test.
- Expected: some hint that the signature groups distinct callers / crashing threads.
- Got: one signature, 558 crashes. Reading dumps one by one showed three unrelated callers: Android `FinalizerDaemon` -> `NativeCallbackDelegate::Finalize` (493, six extension tests), Linux debug content process `imgRequestProxy::DispatchWithTargetIfAvailable` during `nsLayoutStatics::Shutdown` (60, test_ext_contentscript_triggeringPrincipal.js), Android `IPC I/O Parent` -> `GPUProcessHost::OnChannelConnected` (5 singletons).
- Workaround: ~15 `fx-tests crash` calls. Grouping by crashing thread name + first non-assertion frame (frame 3 here) would have answered it in one command.

## Question: the failing tasks for one crash signature, in one test

- Command: `fx-tests test <path> --task-ids --issue <n>`
- Expected: a way to select the crash by signature.
- Got: `--issue` is the per-test index, which differs from test to test for the same signature (3, 1, 2, 9, ...), so a loop over tests had to scrape the Issues list to find the index first.
- Workaround: `rg 'CRASH   @ NS_DispatchToMainThread'` on the text output to get the index. A `--signature <substr>` (or `--issue-message <substr>`) filter on `fx-tests test --task-ids` would avoid it.
