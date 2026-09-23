## Default session dir not writable in the sandbox

- Command: `profiler-cli load <taskcluster url> --session ...`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call (profile-link.py included). The error message did suggest this, which helped.

## Question: "in what order, and how many ms apart, did these markers happen?"

- Command: `profiler-cli thread markers --category Test --search <test> --list --limit 0`
- Expected: timestamps precise enough to order events in a test that runs in 0.6 s.
- Got: every marker of the test shows `t=1m42s` (whole-second resolution). The failure came 33 ms after the step that started it, but that was invisible in the default output.
- Workaround: `marker info ... --json` / `thread markers --list --json` and a script reading `start`. Showing ms (e.g. `t=1m42.066s`) once the listed markers span less than a few seconds would answer it.

## Question: "what happened across the parent and the content process in this window, in order?"

- Command: `profiler-cli profile markers --search getApplied` (cross-thread) and `thread markers --list` per thread.
- Expected: one chronological list for two threads (parent + tab content process), to match each RDP request on the parent to its handler in the content process.
- Got: `profile markers` groups by thread and has no ms times; `thread markers` is one thread at a time.
- Workaround: dumped `thread markers --list --limit 0 --json` for t-0 and t-228 under the same zoom, then merged them by `start` with a script. A `--threads t-0,t-228` option on `thread markers --list` would have answered it.
