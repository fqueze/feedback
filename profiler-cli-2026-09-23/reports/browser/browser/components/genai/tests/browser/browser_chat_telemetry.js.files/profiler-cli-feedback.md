## Question: "at what millisecond did this instant marker happen?"

- Command: `profiler-cli marker info m-12192 m-10270 m-52 --session browser-browser_chat_telemetry.js-1`
- Expected: a time precise enough to order markers a few ms apart and cite them (e.g. `t=69.587s`).
- Got: `Time: 1m10s (instant)`, and `thread markers --list` also prints `t=1m10s` once past 60 s. Three markers within 1.1 s (String::set at 69.587 s, Preference Write at 71.221 s, TEST-UNEXPECTED-FAIL at 72.279 s) all read as "1m10s"/"1m11s"/"1m12s".
- Workaround: `marker info <m> --json | python3 -c ...` to read `start` (ms) per marker.
- Could have shown: millisecond precision past the one-minute mark (`1m9.587s` or `69.587s`), as it does below 60 s (`t=57.082s`).

## Question: "every value this Glean metric was set to"

- Command: `profiler-cli thread markers --search "genai.chatbot.provider,fog.testResetFOG,TEST-UNEXPECTED-FAIL" --list --limit 0 --session browser-browser_chat_telemetry.js-2`
- Expected: the `String::set` markers, which the list itself prints as `genai.chatbot.provider : custom`.
- Got: none of them; only the testResetFOG and failure markers. `--search genai.chatbot` does match them. The label joins two payload fields (Category `genai.chatbot`, Metric `provider`), and the search matches each field separately, so the full dotted metric name as displayed matches nothing, silently.
- Workaround: search on the category (`genai.chatbot`) and grep the output for the metric.
- Could have shown: match the search against the rendered label too, or accept `metric:provider`-style fields and say so in `--help` for Glean markers.
