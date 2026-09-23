## Question: which document does a marker belong to? (innerWindowID -> URL)

- Command: `profiler-cli thread markers --session S --search 'innerWindowID:4294967338' --list` (also tried the bare term `4294967338`)
- Expected: the markers carrying that innerWindowID, e.g. the `DocumentLoad` for that window and the `RefreshDriverTick waiting for paint` markers.
- Got: `0 markers`, even though `marker info m-501` prints `innerWindowID: 4294967338` under "Fields (raw payload, no schema)". It looks like raw fields of schema-less payloads (NoPayloadUserData) are not searched. There is also no command that lists the profile's pages (innerWindowID -> URL).
- Workaround: `thread markers --search 'name:RefreshDriverTick waiting for paint' --list --limit 0 --json`, grouped by `data.innerWindowID` in Python, then matched against the innerWindowIDs in `name:DocumentLoad --json`.
- What would have answered it: search matching raw payload fields, and either a page/URL column for markers that have an innerWindowID, or a `profile pages` command. Grouping `--group-by field:innerWindowID` with the URL would have answered it in one call.

## Question (review): which process keeps vsync enabled?

- Command: `profiler-cli thread markers --session S --search "PVsync::Msg_Notify" --list --limit 0` on the parent main thread, zoomed to the victim's vsync wait.
- Expected: the vsync notifications counted by the process that receives them, which would name the process pinning vsync in one call.
- Got: one row per IPC marker, thousands of them. The receiving process shows only in each row's `marker info`.
- Workaround: `--json`, then grouping `fields[recvThreadName]` in Python.
- What would have answered it: a `--group-by field:recvThreadName` option on `thread markers`, or IPC markers grouped by message type and peer process in the default summary.
