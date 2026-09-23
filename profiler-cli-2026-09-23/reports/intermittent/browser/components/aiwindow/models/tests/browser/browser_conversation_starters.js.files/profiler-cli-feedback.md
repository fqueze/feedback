## Question: "which marker index is this handle", to check a link's `marker=N` (review-browser_conversation_starters.js)

- Command: `profiler-cli marker info m-26 --session <s>`
- Expected: the marker's index in the thread's marker table, the number a profiler.firefox.com link carries as `marker=N`.
- Got: name, type, time, fields, stack; no index. Checking 20 links meant `marker info <m> --json | python3 -c '...markerIndex...'` per handle.
- Could show: `Index: 148986` in the default header, or an index column in `thread markers --list`.

## `--session` placed before the subcommand is rejected

- Command: `profiler-cli --session S zoom push 26.4,36`
- Expected: same as `profiler-cli zoom push 26.4,36 --session S`.
- Got: `error: unknown option '--session' (Did you mean --version?)`.
- Workaround: put `--session` after the subcommand.

## profile-link.py refuses a session loaded from a profiler.firefox.com link

- Command: `profile-link.py --session S --thread t-0 --range 29.97,30.43 --panel marker-table` after `profiler-cli load '<profiler.firefox.com/from-url/... link>'` (as `review-brief.md` says to load).
- Expected: a link, since the from-url link wraps the raw artifact URL.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself".
- Workaround: reload the raw Taskcluster URL (another ~1 min and 0.6 GB); that load also opened on a content-process thread, not t-0.
