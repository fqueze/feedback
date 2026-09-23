## `test --bugs` says nothing when no bug names the test

- Command: `fx-tests test devtools/shared/commands/resource/tests/browser_resources_error_messages.js --bugs`
- Expected: a Bugs section, or a line saying no bug names this test.
- Got: the same output as without `--bugs`, with no mention of bugs at all, so "no bug" and "the flag was ignored" look the same.
- Workaround: took the silence as "no bug".

## Question: "how often does a tab's content process die at launch, with no dump, across CI?"

- Command: `fx-tests errors --day 2026-09-17 --message "remote browser crashed"`
- Expected: the jobs/tests where browser-test.js logged "remote browser crashed while on <url>".
- Got: "No markers matched". The harness logs it as a multi-line `console.error:` whose first line is empty; the text lands in separate `output` lines, and the resource-usage profile shows the `console.error` marker with an empty Message. So the error index cannot find tab crashes by their text.
- Could have shown: the console.error with its continuation lines joined, so a tab-crash message (and the `FX_CONTENT_CRASH_DUMP_UNAVAILABLE` kind of crash that leaves no minidump, invisible to `crashes`) is searchable.
