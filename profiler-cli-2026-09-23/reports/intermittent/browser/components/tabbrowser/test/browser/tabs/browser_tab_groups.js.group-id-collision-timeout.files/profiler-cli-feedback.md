## `load` reports failure on a slow load, but the session keeps loading

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/ML56zK70SaGuils82VeavQ/runs/0/artifacts/public/test_info/profile_browser_tab_groups.js.json --session <s>`
- Expected: `load` waits until the profile is ready, or exits with a status that says "still loading in the background, poll `status`".
- Got: `Error: Profile load timeout after 60000ms (set PROFILER_CLI_LOAD_TIMEOUT_MS to override)`, yet the daemon kept loading; the next commands in my script failed with `Profile still loading, try again shortly`, and a minute later the session was fine. A script cannot tell this "error" from a real load failure.
- Workaround: `PROFILER_CLI_LOAD_TIMEOUT_MS=600000` on every load, and an until-loop on `profiler-cli status` when it was already too late.

## (review) How far apart two markers are, and their `markerIndex`

- Question: "how many ms apart are these two `TabGroupCreate` markers?" and "is this marker the `marker=N` of the link?"
- Command: `profiler-cli thread markers --search "eventType:TabGroupCreate" --list --limit 0 --session <s>`, then `profiler-cli marker info m-1 m-2 --session <s>`
- Expected: `--list` rows precise enough to subtract (the gaps here are 0.3–0.9 ms), and the marker index in the default `marker info` output.
- Got: every row reads `t=4m31s`, and the gaps are invisible. I needed `marker info --json | python3` to get `start` and `markerIndex` for each of about 40 markers across 8 profiles.
- Could show: a `--time ms` (or more precise) column in `--list`, and `markerIndex` in the default `marker info`.
