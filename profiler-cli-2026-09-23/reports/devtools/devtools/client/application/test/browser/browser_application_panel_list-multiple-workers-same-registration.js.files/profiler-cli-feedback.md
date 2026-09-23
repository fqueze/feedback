## Question: "which IPC messages of type X did this thread send or receive, and when?"

- Command: `profiler-cli thread markers --session <s> --search 'messageType:Cache' --list --limit 0` (parent GeckoMain, zoomed 9.7-10.3 s, profile_browser_application_panel_list-multiple-workers-same-registration.js.json of task VvQPA4zdT72ZK68OqucMjw)
- Expected: rows naming the message type (e.g. `PCacheStorage::Msg_PCacheOpConstructor`), its direction, and its sending thread.
- Got: bare `IPCOut` / `IPCIn` rows with no message type, so each had to be opened with `marker info` one by one. Also, the filter matched rows that were not Cache messages, e.g. `PWindowGlobal::Msg_UpdateBFCacheStatus`, because "Cache" is a substring of "BFCache".
- Workaround: `--json` piped to a python script printing `data.messageType`, `data.direction` and `data.threadId`.
- What the default output could show: the message type, and the direction with the other thread, in the list row of IPC markers.

## Resource-usage profile: no machine CPU track

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/ND5tZ8GtQruPtkstB7Z6qA/runs/0/artifacts/public/test_info/profile_resource-usage.json`, then `profile info` and `counter list`
- Expected: the machine's CPU use over time, to tell whether the machine was saturated during an 11.6 s stall.
- Got: `CPU activity over time: No significant activity.` and `No counters in this profile.`
- Workaround: none. The saturation question stayed unanswered.

## Review: a raw Taskcluster load selects a WebExtensions thread, not the parent main thread (review-browser_application_panel_list-multiple-workers-same-registration.js)

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/ND5tZ8GtQruPtkstB7Z6qA/runs/0/artifacts/public/test_info/profile_browser_application_panel_list-multiple-workers-same-registration.js.json --session review-mwsr-2b`, then `thread markers --search PBackgroundIDBFactory --list --limit 0 --json`
- Expected: the parent process GeckoMain selected, the way loading the profiler link with `thread=0` selects it.
- Got: `Selected thread: t-18 (GeckoMain, WebExtensions)`, and my search printed nothing and no error. I noticed only by running `status`.
- Workaround: `thread select t-0`.

## Review: `profile-link.py` refuses a session loaded from a profiler link

- Command: `python3 profile-link.py --session review-mwsr-2 --marker m-205`, on a session loaded from the report's `profiler.firefox.com/from-url/...` link, as `review-brief.md` says to load it
- Expected: a link for a new observation found while checking.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself`. The profile had to be loaded a second time (about 0.6 GB) to link 2 markers.
- Workaround: stop the session, load the raw Taskcluster URL, and link from that session. `profile-link.py` could take the `from-url` of a profiler link as the raw URL.
