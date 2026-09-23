## Log markers (Test category INFO) print "(empty)" for level and message

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <id>` and `profiler-cli marker info m-550 --session <id>` on a plain-mochitest per-test profile (content process GeckoMain), https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/FlazV7ABTUu7wBdhlUe2kw/runs/0/artifacts/public/test_info/profile_test_cloneElementVisually_resource_change.html.json
- Expected: the INFO line's text, e.g. `add_task | Leaving`.
- Got: `INFO  [(empty)] INFO: (empty)` in the list, and `Level: (empty)` / `Message: (empty)` in `marker info`.
- The `--json` output has `"value": "add_task | Leaving "` with `"formattedValue": "(empty)"`: the formatter drops the value for Log markers.
- Workaround: `marker info --json` per marker, or a jq script over `thread markers --list --json`. The test's own log (add_task entering/leaving) is unreadable in the default output.

## IPC markers in `--list` show no message type

- Question: which IPC messages did the Remote Data Decoder exchange with the content process, in order, around a decode error?
- Command: `profiler-cli thread markers --list --limit 0 --search IPC --session <id>` on t-18 (Remote Data Decoder GeckoMain), zoomed to 19.474,19.53, same profile as above.
- Expected: each row with its `messageType`, e.g. `IPCOut ... PRemoteDecoder::Reply_Decode`.
- Got: 67 rows reading only `IPCIn` / `IPCOut` with a duration; the type is only in `marker info` or in `--group-by field:messageType`, which loses the order.
- Workaround: `--group-by field:messageType` to get handles per type, then reorder by hand from the handle numbers, and `marker info` on the ones that matter.

## Review: which error each media error marker carried, and which link index a handle is (review-test_cloneElementVisually_resource_change.html)

- Question: what error message does each `HTMLMediaElement:Error` marker in the content process carry?
- Command: `profiler-cli thread markers --search "HTMLMediaElement:Error" --list --limit 0 --session <id>` on https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/d1v-Yws1Tu-RtsAQIiBxuw/runs/0/artifacts/public/test_info/profile_test_cloneElementVisually_resource_change.html.json (t-16).
- Expected: each row with its `errorMessage`, e.g. `Failed to open media` or `AppleVTDecoder::OnDecodeError:ffffcd93`.
- Got: 8 rows reading only `error`, with no description. Telling the decode error from 7 unrelated `Failed to open media` errors took `--json` and a script, or one `marker info` per row.
- Second question: does handle m-N match `marker=N` in a report's link? Only `marker info --json` shows `markerIndex`. The default `marker info` output could print it next to the handle.
