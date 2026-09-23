## Stopping one session: `session stop <id>` fails with a misleading error

- Command: `PROFILER_CLI_SESSION_OWNER=review-browser_storage_dynamic_windows.js profiler-cli session stop review-browser_storage_dynamic_windows.js-1`
- Expected: the session stopped, or an error pointing to `profiler-cli stop <id>`.
- Got: `error: too many arguments for 'list'. Expected 0 arguments but got 2: stop, review-browser_storage_dynamic_windows.js-1.`, so `session` seems to fall through to `list`.
- Workaround: `profiler-cli stop <id>` (found in `--help`).
