## `thread markers --search` does not match the text shown for `test` markers

- Command: `profiler-cli thread markers --session <s> --search "PASS —,FAIL —,waiting for vsync" --list --limit 0` on a browser-chrome per-test profile.
- Expected: the per-test `test` markers, which the list shows as `test … PASS — browser/components/…/browser_ext_autocompletepopup.js`, plus the vsync waits.
- Got: only the vsync waits. The `test` markers matched only when I searched a substring of the test path.
- Workaround: search by test file name (`--search browser_ext_commands,...`). That also pulls in every INFO and SubScript marker for those tests.

## Default session directory is not writable under the agent sandbox (review-browser_ext_commands_getAll.js, 2026-09-22)

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session ...`
- Expected: the session starts.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error message names the fix.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call, including `profile-link.py`, which reads the same variable. Neither subagent-brief.md nor review-brief.md mentions it, so every agent loses one round trip finding it.
