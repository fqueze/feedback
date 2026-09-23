## Which Android service slot each test used, and what `ps` listed at a moment

- Questions: (1) which `XpcshellTestRunnerService$iN` slot each test launched on, and (2) which `org.mozilla.geckoview.test_runner:*` processes the harness's `get_process_list` DEBUG markers listed at a given time.
- Command: `profiler-cli thread markers --search "INFO,DEBUG,name:test" --list --limit 0` over a zoom of 12 s.
- Got: the right markers, but each `launch_application` / `get_process_list` message is several KB on one line, so the test name (`_TEST_NAME` near the end) and the relevant `ps` entries could not be read without a script over `--json`.
- Could have shown: a way to print only the regex-matched part of a long marker field (for example `--field-match '<regex>'`), or not cutting the message but wrapping it.
