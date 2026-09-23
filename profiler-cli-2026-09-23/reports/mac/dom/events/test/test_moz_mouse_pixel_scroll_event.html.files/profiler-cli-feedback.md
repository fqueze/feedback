## `thread markers --search name:test --list --limit 0` exceeds 2 minutes on a large content thread

- Command: `profiler-cli thread markers --session <s> --thread t-23 --search name:test --list --limit 0` on a per-test mochitest-plain profile whose content main thread has 371,708 markers (task O65Qjz9ORs-uvnXvhnseRQ, `profile_test_moz_mouse_pixel_scroll_event.html.json`).
- Expected: the `test` markers (one per test run in this browser), in a few seconds.
- Got: did not finish within the Bash tool's 120 s timeout (it did eventually finish in the background). `name:test` also matches every Text marker whose payload `name` contains "test", which is documented but makes the natural query for "which tests ran before mine" both slow and noisy.
- Workaround: `--category Test --search <specific substrings>` finished in well under a minute; for the test order, the resource-usage profile's `test` markers were faster.

## `load` reports a timeout, but the daemon keeps loading and succeeds (review-test_moz_mouse_pixel_scroll_event.html)

- Command: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli profiler-cli load '<profiler.firefox.com from-url link to O65Qjz9ORs-uvnXvhnseRQ profile_test_moz_mouse_pixel_scroll_event.html.json>' --session review-test_moz_mouse_pixel_scroll_event.html-1` (a ~370k-marker per-test mochitest profile).
- Expected: either the load finishes, or a failure that means the session is unusable.
- Got: `Error: Profile load timeout after 60000ms (set PROFILER_CLI_LOAD_TIMEOUT_MS to override)`, yet the daemon was still loading. `status` then answered `Profile still loading, try again shortly`, then `Timed out after 30000ms waiting for the daemon ... to answer`, and a minute later the session was ready at the link's thread. The error reads as a failure, and a caller that reloads on it would load the profile twice.
- Workaround: poll `status --session <s>` until it stops saying "still loading" or "Timed out"; later loads with `PROFILER_CLI_LOAD_TIMEOUT_MS=500000` returned normally.
