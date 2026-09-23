## `zoom push` accepts `13m0s,14m10s` and silently zooms to a 1 s range

- Command: `profiler-cli zoom push 13m0s,14m10s --session browser_pbm_cleanup.js-1`
- Expected: either the 13m0s–14m10s range (the format `thread markers` prints times in), or an
  error.
- Got: success, with the view shown as `ts-1→ts-2 (1s)`; the next `thread markers` query matched
  nothing, which looked like an absence of markers.
- Workaround: `zoom clear`, then `zoom push 780,850` in seconds.

## Default session directory not writable in a sandbox

- Command: `profiler-cli load <url> --session browser_pbm_cleanup.js-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error message
  gave the fix (`PROFILER_CLI_SESSION_DIR`), so this cost one retry only.

## (review) `✓` "has stack trace" on C++ warning markers whose stack is one null frame

- Command: `profiler-cli thread markers --session review-browser_pbm_cleanup.js-1 --search browser_navigation_fetch_fault_handling --list --limit 0`,
  then `profiler-cli marker stack m-17 --session review-browser_pbm_cleanup.js-1`
- Expected: `✓` only on markers with a stack I can read. Here that means the `'mClosed'` C++
  warning in a resource-usage profile, where I wanted to know which caller's transaction was refused.
- Got: `✓` in the list, but the stack is a single `[1] unknown!null` frame (`"name": null` in
  `--json`). Every C++ warning marker in these profiles looks the same.
- Workaround: none needed. It cost one call. Showing `✗`, or "stack: empty" for a stack of
  null frames, would save it.
