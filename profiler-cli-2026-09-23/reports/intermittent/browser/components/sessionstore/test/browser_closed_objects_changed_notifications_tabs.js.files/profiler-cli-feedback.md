## Question: "when was this task queued, and which tasks were ahead of it in the main-thread queue?"

- Command: `profiler-cli thread markers --search "TaskController::AddTask,name:Runnable" --list --limit 0 --session <s>` in a 75 ms zoom.
- Expected: a way to see, for each `Runnable` (Task) marker, when it was added (the matching `TaskController::AddTask` marker, whose `flow` equals the Runnable's `task` field) — i.e. the queue order that decides which of two same-priority tasks runs first.
- Got: two unrelated flat lists; the Runnable's `task` and the AddTask's `flow` are shown as raw hex, and matching them by eye across hundreds of rows is impractical. Also the ids are pointer values that get reused, so a naive text search across the whole profile matches unrelated tasks (`--search b45a47efb33014570` hit 5 different tasks at different times).
- Workaround: `--json` and a Python script pairing each Runnable with the latest preceding AddTask of the same flow id. That answered "the timer's runnable was queued between 24.8205 and 24.8214" — the decisive observation of this report.
- What the default output could show: on `marker info` of a Task marker, "queued at t=… (m-N)" when an AddTask with the same flow precedes it on the thread, or "queued from another thread" otherwise.

## Question: "when, on the profile's timeline, was this IPC message sent and received?" (review-browser_closed_objects_changed_notifications_tabs.js)

- Command: `profiler-cli marker info m-4 --session review-bcocnt-1` on an `IPCOut PBackground::Reply_GetSessionStorageManagerData` marker.
- Expected: every time shown on the profile's timeline, the one `Time:` and the other markers use.
- Got: the "Other payload fields (no schema)" section shows `startTime: 24821.077827` and `endTime: 24835.740327`, but the marker's own start is 24820.839. The payload times have not been shifted to the profile's zero: they are off by the "Uptime" that `profile meta` prints (0.24 ms in task a1dvrX24STWN1UKmFfuIrQ, 10.2 ms in BWxT-AkKRySggNzBTAWdkA). Nothing flags this. The original report quoted those raw times as timeline times. In the 10 ms case that would put a message's receipt after the task that handled it.
- Workaround: subtract the `profile meta` uptime, or use only the marker's `start`/`duration`.
- What the default output could show: payload timestamps converted to the profile's timeline (or labeled "raw, +0.24 ms"), and the send/transfer/recv phases as profile times.
