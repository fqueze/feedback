## Question: "which other tests saw a content tab crash (`remote browser crashed while on`)?"
- Command: `fx-tests errors --message "remote browser crashed" --group-by test`
- Expected: rows for tests whose logs carry that console.error.
- Got: "No markers matched" (2026-09-20). The console.error is multi-line (`console.error:` / `  remote browser crashed while on` / `  about:blank`), so presumably only the first line is indexed as the message.
- Workaround: none; left as "what would settle it".
