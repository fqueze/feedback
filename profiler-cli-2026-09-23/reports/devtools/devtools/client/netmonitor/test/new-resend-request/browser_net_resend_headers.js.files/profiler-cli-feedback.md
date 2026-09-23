## Question: "the POST requests in this profile"

- Command: `profiler-cli thread markers --category Network --search "requestMethod:POST" --list --limit 0 --session <s>`
- Expected: the `Load N: <url>` Network markers whose payload has `requestMethod: POST` (`marker info` shows the field, under "Fields (raw payload, no schema)").
- Got: `0 markers`. A bare `--search POST` matched URLs containing "post" (html_post-raw-test-page.html) instead of the method. `thread network` does not show the method either.
- Workaround: `--json` and a Python filter on `data.requestMethod`. `thread network` could print the method next to the status, and field search could cover raw payload keys of schema-less markers.

## `marker info --json` with several handles drops handle and payload

- Command: `profiler-cli marker info m-1037 m-1036 m-1038 --json --session <s>`
- Expected: one record per handle with its handle and the IPC fields the text output shows (Type: PHttpBackgroundChannel::Msg_OnStatus, sendStartTime, recvEndTime...).
- Got: records whose `handle` and `data` came out as null in my reading (keys under another name?), so I could not tell which record was which.
- Workaround: the text output of the same command.
- Correction to the entry above: nothing is dropped. `marker info --json` names them `markerHandle` and `fields`, where `thread markers --list --json` names them `handle` and `data`. The problem is the two shapes disagreeing, so a script written against one reads nulls from the other.
