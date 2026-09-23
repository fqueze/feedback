## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=test_keywords.js profiler-cli load <taskcluster URL> --session test_keywords.js-1`
- Expected: the session loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a clear hint to set `PROFILER_CLI_SESSION_DIR`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every `profiler-cli` and `profile-link.py` command (the directory already held other agents' sessions). One wasted call; the hint was good. Falling back automatically to a writable temp dir, or the brief naming the variable, would save it.


## (review) Same session-directory failure hit the reviewer

- Command: `PROFILER_CLI_SESSION_OWNER=review-test_keywords.js profiler-cli load <profiler link> --session review-test_keywords.js-1`
- Expected: the session loads.
- Got: the same `EPERM ... mkdir '/Users/florian/.profiler-cli'`. `review-brief.md` does not name `PROFILER_CLI_SESSION_DIR` either, so each agent pays for this once.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`.
