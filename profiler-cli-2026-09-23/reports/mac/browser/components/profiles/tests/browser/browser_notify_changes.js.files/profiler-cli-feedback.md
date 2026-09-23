## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=browser_notify_changes.js profiler-cli load "<taskcluster profile URL>" --session browser_notify_changes.js-1`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, followed by `Unknown session ... no metadata found` from the next command.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call, including `profile-link.py`. The error message suggested this, so the cost was one round trip. The brief could name the variable next to `PROFILER_CLI_SESSION_OWNER`.

## (review) Same EPERM on the default session directory, hit again by the reviewer

- Command: `PROFILER_CLI_SESSION_OWNER=review-browser_notify_changes.js profiler-cli load "<taskcluster profile URL>" --session review-browser_notify_changes.js-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. Cost one round trip. Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`.

## (review) Question: how many samples are in this range?

- Command: `profiler-cli thread samples-top-down --include-idle` after `zoom push 4.160,4.180`
- Expected: a sample count, so a report's "the only sample in 4.160–4.180 is X" can be checked.
- Got: percentages only (100%), with no count, so a single sample and many identical samples look the same.
- Could show: the number of samples (total and idle) in the header of the samples commands.
