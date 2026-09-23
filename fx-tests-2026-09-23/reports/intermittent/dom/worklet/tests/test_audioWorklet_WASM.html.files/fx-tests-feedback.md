# fx-tests feedback (test_audioWorklet_WASM.html)

## Question: "a passing run of this test, on the failing config, near the failing push"

- Command: `fx-tests test dom/worklet/tests/test_audioWorklet_WASM.html --task-ids --limit 0`
- Expected: some way to get the task ID of a *passing* execution on the same config (for comparing logcat / timing with a good run).
- Got: only failing task IDs. Android chunks move between pushes, so the same chunk number on the previous push did not contain the test.
- Workaround: queried Treeherder's jobs API for 76 nearby jobs of the config family and grepped each job's `manifests.list` for `dom/worklet/tests` (about 80 HTTP requests).
- What would have answered it: `--task-ids --passing` (or `--coverage --task-ids`) listing a few passing tasks per config.

## `fx-tests crash --all-threads`: the "blocked" heuristic flags idle threads, misses the stuck one

- Command: `fx-tests crash W7i92NMQQqW_tNRJJuib5g 1545fdfc-f2de-0b7c-e2e9-2207439e2c40 --all-threads`
- Expected: the hang's culprit highlighted.
- Got: `Waiting on a lock: #15, #23, #25, ...`: 12 threads, all ordinary idle event loops (`ThreadEventQueue::GetEvent` / `nsAppShell::ProcessNextNativeEvent` condvar waits). The actual stuck thread, #58 `AudioIPC Server` spinning in `wait_for_state_change` <- `aaudio_stream_stop` <- `drop_in_place<ServerStream>`, was not flagged.
- Suggestion: treat a condvar wait under `ThreadEventQueue::GetEvent` / `nsAppShell::Queue::Pop` / `event_base_loop` / `EventLoop::poll` as idle, not blocked. Flag threads whose innermost Mozilla frame is not an event-loop wait, or that sit inside a `Drop`/destroy or a `wait_for_*` helper.

## `fx-tests task --profiles` on Android: no per-test profile, but no pointer to what exists instead

- Command: `fx-tests task W7i92NMQQqW_tNRJJuib5g --profiles`
- Got: "No failing test named a per-test profile in this job." On Android the useful evidence was in `public/test_info/logcat-emulator-5554.log` (device-time GeckoDump lines, AAudio state changes) and the parent minidump.
- Suggestion: on Android jobs, list the logcat artifact URL (and the minidump IDs, already known to `fx-tests test --task-ids`) next to that line. Also mention that the resource-usage profile there has no CPU tracks (only a `mach` thread with markers), so "was the machine saturated" cannot be answered from it.
