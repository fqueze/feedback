## Long session name exceeds the Unix socket path limit, and the suggested fix is the directory already in use

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli profiler-cli load <taskcluster URL> --session test_bug1293174_implicit_pointer_capture_for_touch_1.html-1`
- Expected: the session loads (the name follows the `<report name>-1` convention of the brief), or an error suggesting a shorter session name.
- Got: "The Unix socket path for this session is 105 bytes, over this platform's 103-byte limit ... Use a shorter session directory, for example: PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli" — the exact directory already set, so following the advice changes nothing. The session name is the long part.
- Workaround: shorter session name (`b1293174t1-1`).
- Suggestion: hash or truncate the socket file name (keep the session id in metadata), or suggest a shorter `--session` when the name is the long part.
