## `fx-tests task --profiles` does not list the hang profile of an application timeout

- Command: `fx-tests task NMbnCN1zQtmQXT0c9L3UoA --profiles` (also eQt7Hs7TSpWl5VNXPAPeEw, L16VKJ4YRw-qgKXMOBHcEw)
- Expected: for the failure "application timed out after 370.0 seconds with no output" on macOS, the profile the harness captured from the hung browser, `public/test_info/profile_0_<pid>.json`.
- Got: "No failing test named a per-test profile in this job." The artifact `public/test_info/profile_0_3069.json` exists and is exactly the profile of the hung browser session (parent main thread stuck for 6m44s).
- Workaround: listed the task's artifacts with the Taskcluster API and picked `profile_0_*.json`.
- Question it should answer: "the profile of the browser that hung in this test".

## `fx-tests test --task-ids` shows no minidump IDs for the Windows hang dumps

- Command: `fx-tests test browser/components/tests/browser/browser_browserGlue_client_association_ping.js --task-ids --limit 0`
- Expected: the help says it prints "the minidump IDs of any crashes"; Windows jobs are CRASH+TIMEOUT with dumps written by mozcrash (e.g. WgE0R749TTWIoeD7iLQtmg, parent dump 9739bac6-8f2a-43c2-b2c6-0beedc1f0f6b).
- Got: task IDs only, no minidump IDs; `fx-tests task` also printed none.
- Workaround: downloaded `live_backing.log` and grepped `PROCESS-CRASH` lines to find the dump of the parent pid; `fx-tests crash <task> <dump>` then worked well.
- Question: "the hang dump of the parent process for this timeout".
