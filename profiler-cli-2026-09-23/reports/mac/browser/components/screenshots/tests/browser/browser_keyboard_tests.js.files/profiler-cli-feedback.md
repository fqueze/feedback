## Question: which markers in this time range have a stack going through function X (here downloads.js / head.js!cleanup)?

- Command: `profiler-cli zoom push 13.390,13.470; profiler-cli thread markers --has-stack --list --limit 0 --json`, then a shell loop running `profiler-cli marker stack m-N` on each of the ~140 handles and grepping the frames.
- Expected: a way to filter markers by a frame in their captured stack, e.g. `thread markers --has-stack --stack-search downloads.js` (or the top few JS frames in `--list` output).
- Got: `--search` matches only name/category/payload fields, and `--json` flatMarkers carry no stack, so the only way was one `marker stack` call per marker (a few minutes).
- Workaround: the loop above.

## Question: how late did each setTimeout callback run?

- Command: `profiler-cli thread markers --search "name:setTimeout callback" --list --limit 0 --json`, then `marker stack` per handle to read `Captured at:` (the setTimeout call time) and compute run time - (captured + interval).
- Expected: the capture time of a marker's stack in `--list`/`--json` output when it differs from the marker start (for setTimeout callback markers it is the scheduling time), or a derived "lateness" column.
- Got: `Captured at` only appears in `marker stack` output.
- Workaround: a Python loop over 36 handles calling `marker stack`.

## Session directory not writable in the agent sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <url> --session ...`
- Got: `Cannot create the profiler-cli session directory /Users/florian/.profiler-cli. Permission denied.`
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command (the error message said so, cost one retry).

## (review) profile-link.py refuses a session loaded from a profiler.firefox.com link

- Command: `profiler-cli load "<the report's profiler.firefox.com/from-url/... link>" --session review-...-1` (as review-brief.md says: load the link to check it), then `profile-link.py --session review-...-1 --marker m-132`.
- Expected: a link for a marker the report should have linked, from the session that is already loaded on that profile; the from-url link contains the raw artifact URL.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link.`
- Workaround: load the same profile a second time from its Taskcluster URL just to build links (another 0.6 GB session).
