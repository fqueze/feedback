
## review-test_applicationacc.xhtml: JS frame names end in `[N]`, which reads as a line number

- Command: `profiler-cli marker stack m-7 --session review-test_applicationacc.xhtml-1` (and `marker info --json`, `stack.frames[].name`).
- Expected: a JS frame's line (and column), or no number, or a label saying what the number is.
- Got: `chrome://.../tree/test_applicationacc.xhtml[55]` for a call on line 24; the same call is `[540]` in the retry's profile. `common.js[77]` for a call on lines 110-114. The report under review took `common.js[77]` for "common.js line 77".
- Workaround: read the file at the revision and find the call by hand.

## review-test_applicationacc.xhtml: default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster url> --session ...`
- Expected: the session starts.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call.
