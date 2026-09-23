## Question: "the hang dump behind this `application timed out ... with no output`"

- Command: `fx-tests test browser/base/content/test/browser-webrtc/browser_stop_streams_on_indicator_close.js --task-ids --limit 0` (and `fx-tests task Ro-Jj1IpTG6_cPtNHsWumA --json`)
- Expected: `--help` says `--task-ids` prints "the minidump IDs of any crashes"; `task` lists these failures as `CRASH, TIMEOUT ... @ futex_wait`, so the minidump ID of the harness's hang dump.
- Got: no minidump ID anywhere (the JSON has `message: null`, `testProfiles: []`).
- Workaround: `curl .../runs/0/artifacts` and pick the `public/test_info/<uuid>.dmp` by hand, then `fx-tests crash <task> <uuid>`.
- What could have shown it: the dump IDs next to the TIMEOUT row, as for crashes.

## Question: "is there any profile of this hang"

- Command: `fx-tests task Ro-Jj1IpTG6_cPtNHsWumA --profiles`
- Expected: the profile the harness captures of the hung browser on Linux (`public/test_info/profile_0_<pid>.json`, 47 MB, covers the whole session up to the kill).
- Got: "No failing test named a per-test profile in this job." It was the only profile of the hang, and the key evidence here.
- Workaround: listed the Taskcluster artifacts by hand.

## `crash --all-threads` flags idle event-loop threads as blocked

- Command: `fx-tests crash Ro-Jj1IpTG6_cPtNHsWumA 1686f6b4-3ad6-3971-f968-56676bed6479 --all-threads`
- Expected: the few threads actually waiting on a lock (here #0 in `OffTheBooksMutex::Lock`, #96 in `CamerasChild::DispatchToParent` waiting on a Monitor).
- Got: "Waiting on a lock: #0, #10, #13, #19 ... (57 threads)"; nearly all are idle in `ThreadEventQueue::GetEvent` / `nsThreadPool::Run` condvar waits.
- Workaround: grep the saved output for the frames of interest.
- Suggestion: treat `ThreadEventQueue::GetEvent`, `TaskController::GetRunnableForMTTask`, `nsThreadPool::Run`, `WorkerPrivate::WaitForWorkerEvents` as idle.

## Missing processed dump reported as missing artifact

- Command: `fx-tests crash bVevkfNBSoakVHaAh3lxLw 36544a17-fab2-4997-98dd-2dcecdb49d75`
- Expected: either the stack, or "the .dmp is there but was never processed (no .json)".
- Got: "no minidump ... the artifact is not there. Taskcluster expires artifacts ..." although `public/test_info/36544a17-....dmp` is listed in the task's artifacts (only the `.json` is missing). Also the parent process's dump of that job (27cde4e9, pid 7292 in the log) was not uploaded at all; its stack is only in live_backing.log.
- Workaround: read the symbolicated stacks from `public/logs/live_backing.log`.
