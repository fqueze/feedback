## Question: did a test server (ssltunnel / pywebsocket) report an error while this test ran?

- Command: `fx-tests task eHnpU9yrQxW1zOq53Hr5Pw.0 --profiles`, then the resource-usage profile (`profiler-cli thread markers --search "real server" --list`), then `fx-tests errors --day 2026-09-14 --message "establish a connection"`.
- Expected: ssltunnel's `could not open connection to the real server` line, which is in the job log right next to the failure (live_backing.log line 19013), to show up in the resource-usage profile's `output` markers, or `fx-tests errors` to find the test's `Console message: [JavaScript Error: "Firefox can't establish a connection to the server at ws://..."]`.
- Got: neither. The resource-usage profile has no marker for the ssltunnel stdout line, and `errors` has no match for the console message (it lives in an INFO `Console message:` line, not a JavaScript error marker).
- Workaround: downloaded `public/logs/live_backing.log` and grepped it. That line was what decided the diagnosis.
