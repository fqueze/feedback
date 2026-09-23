# profiler-cli feedback (review-browser_startup_content_mainthreadio.js)

## Question: does this link's `marker=N` point at the marker the report quotes?

- Command: `profiler-cli load '<profiler.firefox.com link with marker=30157>' --session ...`, then `profiler-cli marker info m-82 --session ...`
- Expected: `load` to say which handle the link's `marker=N` is (it knows the thread), or plain `marker info` to print the marker index.
- Got: `load` prints only the thread and range. Plain `marker info` has no index. I needed `marker info <m> --json` piped to a Python one-liner, once per handle, to read `markerIndex` for 25 links.
- Workaround: `for m in ...; do profiler-cli marker info $m --json | python3 -c '...markerIndex...'; done`.
- What would answer it: an `Index: N` line in `marker info`, or `load` printing "link marker=N is m-K: <name> <text>".

## profile-link.py refuses a session loaded from a profiler link

- Command: `python3 profile-link.py --session review-...-3 --marker m-120` on a session that `profiler-cli load <profiler.firefox.com link>` had loaded, as review-brief.md says to do.
- Expected: a link.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link."
- Workaround: stopped the session and loaded the same profile again from the raw Taskcluster URL (another full load), just to build two links.
- Note: the `from-url` part of a profiler link holds the raw URL, so the script could recover it from there.

## Default session dir is not writable in the agent sandbox

- Command: `profiler-cli load <url> --session ...`
- Got: EPERM creating `~/.profiler-cli`. The error message itself suggested `PROFILER_CLI_SESSION_DIR`, which worked (`/Users/florian/.sandbox/tmp/profiler-cli`, which other agents already use). One retry. The briefs do not mention this variable.
