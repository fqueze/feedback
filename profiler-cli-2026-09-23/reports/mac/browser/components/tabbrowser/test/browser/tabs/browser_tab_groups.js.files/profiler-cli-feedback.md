## Marker times in text output are rounded to the second

- Command: `profiler-cli thread markers --category Test --search browser_tab_groups.js --list --limit 0 --session browser_tab_groups.js-1`
  (and `marker info m-72 ... m-80`)
- Expected: times precise enough to order and space markers, e.g. `t=200.0158s`.
- Got: every marker in a 3m24s profile prints `t=3m20s` / `t=3m21s`; `marker info` prints
  `Time: 3m20s (instant)`. The whole failing subtest (collapse, expand, failed assert, 10 ms
  apart) collapses to one value, so the text output cannot say what happened in which order,
  nor feed `zoom push` a range.
- Workaround: `--json` and read `flatMarkers[].start` (ms) with a script.
- Would help: millisecond precision once the profile is longer than a minute (e.g. `t=200.016s`),
  at least in `marker info` and `--list`.

## Default session directory is not writable in this sandbox

- Command: `profiler-cli load <url> --session browser_tab_groups.js-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, and then a 30 s
  timeout on the next command before the daemon answered.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call.

## `load` of a 112 MB (gzip) profile dies silently

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/FR9lvC3cQAWVF1sKIGsepg/runs/0/artifacts/public/test_info/profile_browser_tab_groups.js.json --session browser_tab_groups.js-4`
  (one other session of mine loaded, machine load average ~20)
- Expected: a loaded session, or an error saying why not.
- Got: first try exit 1 after printing only `Loading profile from ...`; second try exit 0 with the
  same single line. Either way the session then does not exist (`Unknown session ... no metadata
  found`), and the daemon log ends at `Fetching profile from ...` with no error.
- Workaround: none; skipped that profile.
- Would help: the daemon logging why it exited (OOM, fetch failure), and `load` returning non-zero
  with that reason.

## `marker stack` one call per marker

- Question: "which JS stack dirtied style in this 70 ms window" (164 SetNeedStyleFlush markers).
- Command: `thread markers --search SetNeedStyleFlush --list --json`, then one `marker stack m-N`
  per marker from a script (164 daemon round-trips on a loaded machine).
- Would help: `thread markers --list --with-stacks` (top N frames per row), or `marker stack`
  accepting a range like `marker info` does.

## (review) A raw-URL `load` selects a content thread, and later searches come back empty with no warning

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/T-M99wlRSQKtm75SYMOxnw/runs/0/artifacts/public/test_info/profile_browser_tab_groups.js.json --session review-browser_tab_groups.js-3r`, then `thread markers --search TabGroupCreate --list --limit 0 --json`
- Expected: the parent process GeckoMain selected, as when the same profile is loaded from its profiler link (`thread=0`), or else a note saying which thread got selected.
- Got: `t-223 (GeckoMain, Privileged Content)` was selected, and the search printed nothing. That looked like "no such marker" until `status` showed which thread it was on.
- Workaround: `thread select t-0` after every raw-URL load.
- Would help: default to the parent process main thread for a test profile. Also, when a search matches nothing on the selected thread, say which thread was searched, or how many matches the other threads have.
