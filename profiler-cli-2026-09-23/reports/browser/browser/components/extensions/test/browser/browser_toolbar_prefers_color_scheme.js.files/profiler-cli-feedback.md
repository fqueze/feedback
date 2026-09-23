## Marker times in text output are rounded to whole seconds in long profiles

- Command: `profiler-cli thread markers --session <s> --category Test --search browser_toolbar_prefers_color_scheme --list --limit 0` (and `marker info m-11`), on an 8m23s profile.
- Expected: millisecond timestamps, as on short profiles (`t=9.640s`).
- Got: `t=8m14s` for every marker of the test, and `Time: 8m23s` in `marker info`, so the order and gaps between the test's last subtest, `checking for open popups` and the vsync failure could not be read.
- Workaround: `--json` and read `start`.

## `zoom push` and printed marker times use different zeros

- Command: `zoom push 493.898,502.583` after reading `start: 493898.167` for a marker in `--json`.
- Expected: the zoom to start at that marker.
- Got: `currentViewRange.start` 493905.947, i.e. the zoom is relative to `rootRange.start` (7.947 ms here) while printed `t=` and `--json` `start` are absolute. An ~8 ms shift, enough to cut off a marker at the edge.
- Workaround: subtract `rootRange.start` by hand.

## (review) Loading the raw Taskcluster URL timed out where the profiler link had loaded

- Command: `profiler-cli load 'https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/QpSrFmkpR1Gt5mBA-g7xLw/runs/0/artifacts/public/test_info/profile_browser_toolbar_prefers_color_scheme.js.json' --session <s>`, minutes after loading the same profile from its profiler.firefox.com link without trouble.
- Expected: the profile to load, as it had from the link.
- Got: `Error: Profile load timeout after 60000ms`.
- Workaround: `PROFILER_CLI_LOAD_TIMEOUT_MS=300000`.
