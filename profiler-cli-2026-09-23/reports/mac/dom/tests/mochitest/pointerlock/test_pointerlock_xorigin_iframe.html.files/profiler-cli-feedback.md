## Log markers (INFO) render as "(empty)" although their payload has the text

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session <s>` on the mochi.xorigin-test GeckoMain thread (t-18) of
  https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/JR9efRP4SZCklRW5U5dA-A/runs/0/artifacts/public/test_info/profile_test_pointerlock_xorigin_iframe.html.json
  (also `marker info m-324`).
- Expected: `INFO  Test finish`, `INFO  file_pointerlock_xorigin_iframe.html: ...` etc.
- Got: every INFO row is `[(empty)] INFO: (empty)`; `marker info` shows `Level: (empty)`, `Message: (empty)`.
- `marker info m-324 --json` shows `"value": "Test finish"` with `"formattedValue": "(empty)"` for the `message` field (and `"value": "INFO"` / `"(empty)"` for `level`): the formatter drops the value.
- Workaround: `marker info --json` per marker, or `thread markers --list --json` and read `fields[].value`. Costly: the test's own `info()` lines are the test's log and none of them are readable in the default output.

## Question: which IPC messages went to / came from one process in a time range?

- Command: `profiler-cli thread markers --search "name:IPCOut,name:IPCIn" --list --limit 0` on the parent GeckoMain (t-0) of the JR9efRP4SZCklRW5U5dA-A profile, zoomed to 1.85-2.12s.
- Expected: each row to name the message type and the other side, e.g. `IPCOut  PBrowser::Msg_Activate  -> 4501`.
- Got: rows read only `IPCOut` / `IPCIn` with a duration; the message type (`messageType`) and `otherPid` are only in `marker info` or `--json`. `--search messageType:...` works, but you have to already know the message name.
- The popup's own content process (pid 4501) was missing from the profile, so the parent's IPC to it was the only evidence of what it was told; I had to script over `--json` to list `messageType`/`otherPid` per row.
- Workaround: `--list --limit 0 --json | python3 ...` printing `data.messageType`, `data.otherPid`.
- Would have helped: the IPC marker label in list mode carrying `messageType` and `otherPid` (as the profiler UI's tooltip does), and a way to filter IPC markers by `otherPid`.

## `marker info --json` returns a different shape for one handle than for several (review-test_pointerlock_xorigin_iframe.html)

- Command: `profiler-cli marker info m-1503 m-1505 --json --session <s>`, then `profiler-cli marker info m-20823 --json --session <s>`, on https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/UO14ITQ2QVOGI3VSxLVxJg/runs/0/artifacts/public/test_info/profile_test_pointerlock_xorigin_iframe-2.html.json
- Expected: one shape for both, so that one `jq`/python filter reads `markerIndex` whether one handle is passed or several.
- Got: with several handles, `{"type": ..., "requested": ..., "markers": [...], "errors": ...}`; with one handle, the marker record itself at the top level (`{"type": "marker-info", "markerHandle": ..., "markerIndex": ...}`). My filter written for one shape failed on the other twice.
- Workaround: branch on `"markers" in d`.
- Also: in both shapes, an IPC marker's `messageType` and `otherPid` are only in `fields[]`. `data` has neither, although `thread markers --list --json` puts both in `data`.
