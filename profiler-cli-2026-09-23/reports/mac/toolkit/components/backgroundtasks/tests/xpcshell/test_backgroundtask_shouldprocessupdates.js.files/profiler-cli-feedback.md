## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster URL> --session test_backgroundtask_shouldprocessupdates.js-1`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, then `Unknown session ...: no metadata found`. The error does suggest `PROFILER_CLI_SESSION_DIR`, which is what worked.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call (and for `profile-link.py`). Worth mentioning in the subagent brief, or falling back to `$TMPDIR` automatically.

## (review) profile-link.py refuses a session loaded from a profiler.firefox.com link

- Command: `PROFILER_CLI_SESSION_OWNER=... python3 profile-link.py --session review-...-1 --marker m-1`, after `profiler-cli load 'https://profiler.firefox.com/from-url/...'`. The review brief says to load the report's links that way.
- Expected: a link for a new observation in the same profile.
- Got: "session ... loaded https://profiler.firefox.com/... A link needs the raw artifact URL". I had to reload the profile from the Taskcluster URL, and marker handles changed with the reload.
- Workaround: reload from the raw URL. `profile-link.py` could decode the `from-url/` segment of a profiler link itself, since profiler-cli already accepts that form.
