## Log markers (INFO) in mochitest-plain content process show "(empty)" level and message

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session browser-test_multiple_forms.html-1` (thread t-14, http://mochi.test content process), and `profiler-cli marker info m-971`
- Expected: `INFO  expecting a popup` in the list row, and `Message: expecting a popup` in marker info.
- Got: `INFO  [(empty)] INFO: (empty)` and `Level: (empty)`, `Message: (empty)`. `marker info --json` shows `"value": "expecting a popup"` with `"formattedValue": "(empty)"` for the `message` field (and `"value": "INFO"`, `"formattedValue": "(empty)"` for `level`).
- Workaround: `marker info <handle> --json` and read `fields[].value`. Costs one call per INFO marker, which is the test's own narration.
- Profile: https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/NoNHWKntQkmyFdcZxksXcg/runs/0/artifacts/public/test_info/profile_test_multiple_forms.html.json

## Question: "in what order did these markers happen, to the millisecond, late in a long profile?"

- Command: `profiler-cli thread markers --search IPC,ReceiveQuery,SendQuery,DOMEvent,Test --list --limit 0 --session browser-test_multiple_forms.html-1` (zoomed to 350.4,351.5, profile 11m14s long)
- Expected: start times with ms precision, e.g. `350.514s` for the keydown and `350.521s` for `FormAutofill:onFieldsDetectedComplete`: the whole diagnosis is that the second came 7 ms after the first.
- Got: every row printed as `t=5m50s` or `t=5m51s`; past one minute, the list rounds to whole seconds, so ordering within a second can only be taken from the row order.
- Workaround: `--json` piped into a script printing `start/1000` with 4 decimals. Could show `5m50.514s` (or seconds with ms) whenever the view is shorter than a few seconds.

## Question: "which IPC message is this IPCIn/IPCOut row?"

- Same command as above.
- Expected: the message type on each row, e.g. `IPCOut  PWindowGlobal::Msg_RawMessage`, `IPCIn  PBrowser::Reply_NotifyIMEFocus`.
- Got: IPCIn/IPCOut rows have an empty label column (only name, time, duration). `messageType` is in `--json` `fields[]`.
- Workaround: `--json` and a script. Showing `messageType` (and `otherPid`) as the row label would have answered it.

## Question: "which tests ran in this browser before the failing one, and with what status?" (review)

- Command: `profiler-cli thread markers --category Test --search "name:test" --list --limit 0 --session browser-review-test_multiple_forms.html-2`
- Expected: only the per-test `test` markers (`PASS — …test_formautofill_preview_highlight.html`).
- Got: `DocumentLoad`, `FirstContentfulPaint` and so on, because `name:` also matches payload values and every mochitest URL contains "test". Without `--search`, the per-test markers are buried among hundreds of `TEST-PASS`/`INFO` rows.
- Workaround: `--json` and a script keeping `name == "test"`. An exact marker-name filter would answer it, and so would a per-test summary (like the resource-usage profile's).
