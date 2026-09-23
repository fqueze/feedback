## Question: "which tests ran before mine in this browser session, in order?" (review-browser_login_storage_migrator.js)

- Command: `profiler-cli thread markers --category Test --search "name:test" --list --limit 0 --session review-browser_login_storage_migrator.js-1`
- Expected: only the per-test `test` interval markers (one per test file, with PASS/FAIL and path).
- Got: `name:` is a case-insensitive substring match, so it also returns every TEST-PASS / TEST-UNEXPECTED-SKIP / `task` / `fog.testResetFOG` / `TestUtils` marker (thousands on a 3-minute session).
- Workaround: `--search "browser/browser_" --list --limit 0 --json | jq '.flatMarkers[] | select(.name=="test")'`.
- What would have answered it: an exact-name match (e.g. `name:=test`), or a `--name` option.
