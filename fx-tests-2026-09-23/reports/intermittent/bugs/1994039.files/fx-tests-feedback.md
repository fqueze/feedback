## Which tests hit one crash reason (bug 1994039)

Question: which tests crash with "Network cache reported memory consumption is not at 0" (the bug's summary)?

- `fx-tests intermittent --bug 1994039 --since 21 --limit 0`: expected the crashing tests under "Tests named, per annotated job"; got 11 tests (test_backgroundupdate_exitcodes.js 30x, test_URIFixup_info.js 7x, ...) none of which crashed with this assertion. The crashes were all in browser/components/mozcachedohttp/test/unit/test_host_integration.js and test_url_parsing.js, which the output never names: sheriffs star the job-level "xpcshell return code: 0" line and other tests' failures, and the crash itself passed on retry.
- `fx-tests failures --harness xpcshell --message "Network cache reported memory"`: "No failure matched" — the crash reason is not searchable.
- `fx-tests crashes --harness xpcshell --signature NS_DebugBreak`: one row, 1,279 crashes in 73 tests — every NS_DebugBreak assertion lumped together, no crash reason, no test list.
- Workaround: downloaded the 61 annotated jobs' live_backing.log and grepped "INFO crashed process | ... | <test>" lines. What could have shown it: `intermittent --bug` listing the tests whose crash reason matches the bug summary, or `crashes` grouping NS_DebugBreak/MOZ_CRASH by "Mozilla crash reason" with a `--tests` list.
