
## (review) Socket-path-too-long error suggests the directory that just failed

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli profiler-cli load <taskcluster profile URL> --session review-browser_accessibility_panel_toolbar_pref_scroll.js-1`
- Expected: either the session starting, or a suggestion that fixes the error (a shorter directory, or a shorter session name).
- Got: "The Unix socket path for this session is 105 bytes, over this platform's 103-byte limit ... Use a shorter session directory, for example: PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli" -- the same directory I had passed. The preceding EPERM error (default `~/.profiler-cli` not writable in the sandbox) suggests that same directory, so following the first suggestion leads straight into the second error when the session name is long (the skill's `review-<report name>-N` names are ~60 bytes).
- Workaround: `PROFILER_CLI_SESSION_DIR=/tmp/pcli`. The suggestion could compute a directory short enough for the given session name, or say the session name is the part to shorten.

## (review) `screenshots --at <t>` misses the frame listed at exactly t

- Command: `profiler-cli screenshots --at 3.913 -o <dir> --session review-browser_accessibility_panel_toolbar_pref_scroll.js-1` (retry profile of task LHYMBSUkQna5sRfy55AVNg)
- Expected: the win 1 frame that `screenshots --range` lists as `t=3.913s` (8,503 B).
- Got: the previous frame, `t=3.860s` (6,125 B), which does not show the toolbar yet. The listed frame's real start is 3913.29 ms, so `--at` compares against the unrounded time while the listing prints it rounded to 1 ms: a time copied from the tool's own output selects the frame before it.
- Workaround: `screenshots --range 3.85,4.02`. `--at` could treat its argument at the listing's precision, or the listing could print enough digits to round-trip.
