## Default session dir not writable in a sandbox
- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session x`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error message suggests `PROFILER_CLI_SESSION_DIR`, which works. Nothing to change there beyond noting it, since it costs one retry per agent.

## Question: "when did this job's tests start timing out, and was the machine busy then?" (resource-usage profile)
- Command: `profiler-cli thread markers --search name:test --list --limit 0 --json`, then a Python script that buckets markers by start time and `data.status`. Also `--search 'name:CPU Use' --list --limit 0 --json`, bucketed by 10 s on `cpuPercent`.
- The default output lists 1,388 `test` markers one row each, and 1,302 `CPU Use` markers without their values, so it cannot answer either part.
- What would answer it: `thread markers --search name:test --group-by field:status --buckets 10s` (counts per status per time bucket). For CPU, a per-bucket mean/max of `CPU Use` (like `counter info`'s "over time" section) when the resource profile has no counters.

## (review) A bare `-term` exclusion in `thread markers --search` silently matches nothing
- Command: `profiler-cli thread markers --category Test --list --limit 0 --search "-CONSOLE_MESSAGE" --session <s>`
- Expected: the Test markers without `CONSOLE_MESSAGE`, as `-field:value` already does (`--search "-name:CPU Use,-name:Memory"` works), or an error saying a bare exclusion is not supported.
- Got: `No markers match the specified filters.`, which reads as "the test logged nothing".
- Workaround: no `--search`, then `| rg -v CONSOLE_MESSAGE`.

## (review) Question: "did this test's harness thread end right after the test started?" (resource-usage profile)
- Command: `thread markers --search name:test --list --limit 0 --json` and `thread markers --search "will retry" --list --limit 0 --json`, then a Python join on the test name, to get the delay from each `test` marker's start to its `<test> failed or timed out, will retry.` INFO.
- The default output lists the two sets separately. The INFO markers carry the test name only inside their message, so there is no way to line them up with the test they belong to.
- What would answer it: an option on `--search <test>` that prints each match's offset from the start of that test's `test` marker. Or a per-test view of every marker whose text names the test, with offsets.

## (review) Session dir under a report's `.files/` is too long for the socket
- Command: `PROFILER_CLI_SESSION_DIR=<report>.files/review-sessions profiler-cli load <url> --session <report name>-1`
- Got: `The Unix socket path for this session is 196 bytes, over this platform's 103-byte limit`. The message names a short directory that works, so this cost one retry.
- Suggestion: keep the socket in a short temporary directory whatever the session directory is.
