## Question: "what did the test's info() calls log?" (content-process mochitest INFO lines)

- Command: `PROFILER_CLI_SESSION_OWNER=test_peerConnection_stats_oneway.html profiler-cli thread markers --session test_peerConnection_stats_oneway.html-1 --thread t-27 --category Test --list --limit 0` (profile of task CTytY1WBToyb8H1JTHED6A)
- Expected: INFO rows showing the message text, e.g. `INFO: Skipping ICE Server for this test`, `Got stats: {...}`.
- Got: every one of the 432 INFO rows reads `[(empty)] INFO: (empty)`. `marker info m-21 --json` shows the payload is there (`level: "INFO"`, `message: "Skipping ICE Server for this test"`) but its `formattedValue` is `(empty)`. `--search "Skipping ICE"`, `--search "message:Got"` and `profile logs --search "Got stats"` also do not match these markers, so the text is neither shown nor searchable.
- Workaround: none for INFO lines; used the TEST-PASS markers (`Stat field ... is allowed. {<stat JSON>}`) that happen to embed the stat.
