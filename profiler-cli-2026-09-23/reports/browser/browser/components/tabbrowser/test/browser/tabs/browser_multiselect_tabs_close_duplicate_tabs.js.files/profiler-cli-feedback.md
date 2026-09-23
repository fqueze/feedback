## Question: "which tests ran in this browser session, with their status?"

- Command: `profiler-cli thread markers --session <s> --category Test --search "— browser/" --list --limit 0`
- Expected: the `test` markers (their text is `PASS — browser/...`).
- Got: `No markers match the specified filters.` `--search name:test` matched 1489 markers (every Test marker), as the guide warns that `name` is also a Text payload key.
- Workaround: `--category Test --list --limit 0 | rg '  test   '`. A way to filter on the marker name only (e.g. `--name test`) would answer it directly.
## Loading a large per-test profile times out (review)

- Command: `profiler-cli load "<profiler.firefox.com from-url link to profile_browser_multiselect_tabs_unload_telemetry.js.json of CHZDjjiuQJqhXjvVR7tCyQ>" --session <s>`, a profile with 2M markers.
- Got: `Error: Profile load timeout after 60000ms (set PROFILER_CLI_LOAD_TIMEOUT_MS to override)`, then `Profile still loading, try again shortly` on the next command.
- Workaround: polled until it answered, and set `PROFILER_CLI_LOAD_TIMEOUT_MS=400000` for the reload.
