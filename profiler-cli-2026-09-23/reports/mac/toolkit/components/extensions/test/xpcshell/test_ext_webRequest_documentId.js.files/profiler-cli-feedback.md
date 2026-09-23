## Daemon dies silently while loading a 62 MB (gzip) per-test profile

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/pcli PROFILER_CLI_SESSION_OWNER=test_ext_webRequest_documentId.js profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/O744U7oVQTqihH8K2LkphA/runs/0/artifacts/public/test_info/profile_test_ext_webRequest_documentId.js.json" --session test_ext_webRequest_documentId.js-3` (three tries, the last with no other session of mine loaded).
- Expected: the profile loads, or an error saying why (out of memory, size limit).
- Got: `Error: Session ... is not reachable. ... The daemon exited without cleaning up.` The session log ends at `Fetching profile from ...` with no error. The 18 MB and 19 MB (gzip) profiles of the same test loaded fine; this one is 62 MB gzip, most of it likely `DummyEvent`/`TaskController::AddTask` markers (the other two had 3.5M markers each).
- Workaround: none; read two profiles instead of three.

## The default session directory is not writable in a sandbox

- Command: `profiler-cli load <url> --session <id>` without `PROFILER_CLI_SESSION_DIR`.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a clear hint to set `PROFILER_CLI_SESSION_DIR`. Cheap to work around; noted because `profile-link.py` must then get the same variable.

## Question: which IPC messages did the parent exchange with one child pid after time T?

- Command: `profiler-cli thread markers --category IPC --list --limit 0` (after `zoom push 13.4,63`).
- Its list rows only say `IPCOut` / `IPCIn` with no message type and no other pid, so the answer (PBrowser::Msg_Destroy sent to pid 17161 at 13.982 s, PBrowser::Msg___delete__ back at 61.617 s) needed `--json` and a script over `data.messageType` / `data.otherPid`.
- What the output could have shown: the message type and the peer pid in the list row, e.g. `IPCOut  PBrowser::Msg_Destroy -> 17161`, and a way to filter by peer pid (`--search otherPid:17161` did not seem obvious).

## Question: the machine's CPU use over a test's time window (resource-usage profile)

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0` on `profile_resource-usage.json`.
- 20,551 `CPU Use` markers; the answer ("100% for the whole 62 s of this test, and for the whole parallel phase") needed `--json` and a script averaging `data.cpuPercent` per window. `counter list` says "No counters in this profile".
- What the output could have shown: an aggregate (min/avg/max of `CPU Percent`) for the markers in the current zoom.

## Question (review): which marker handle does a link's `marker=N` point at?

- Command: `profiler-cli load "<profiler.firefox.com link with ...&marker=1259774...>" --session <id>`, then checking each link a report cites.
- Expected: `load` (or `status`) to say which handle the URL's `marker=N` selected, or a lookup such as `marker info --index N`.
- Got: the session status names the thread only. The only way to match an index was to guess candidate handles from `thread markers --search` and run `marker info <m-…> --json` on each to read `markerIndex`: about 40 calls over three profiles, several misses (the index of a `ReceiveMessage` next to an `IPCIn`, a `PASS` next to its `TEST-PASS` twin).
- Workaround: as above.
