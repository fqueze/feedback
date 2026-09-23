## Socket-path error suggests the directory already in use (review-browser_dbg-javascript-tracer-sidebar-values-search.js)

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli profiler-cli load <url> --session review-browser_dbg-javascript-tracer-sidebar-values-search.js-1`
- Expected: a fix that works, such as "use a session name of at most N characters".
- Got: "The Unix socket path for this session is 109 bytes, over this platform's 103-byte limit ... Use a shorter session directory, for example: PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli", which is the directory already set. The brief's `review-<report name>-1` naming exceeds the limit for any long test file name.
- Workaround: shorter session name (`review-tracer-svs-1`), with the full report name kept as `PROFILER_CLI_SESSION_OWNER`.
