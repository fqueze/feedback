## Precise start times of test markers in a long resource-usage profile

- Question: "how many seconds separate the start of test A and test B in this job?"
- Command: `profiler-cli thread markers --search "browser_searchRestrictToken_privateWindow,...,browser_search_lastused_pref" --list --limit 0 --session <s>` on a 28-minute resource-usage profile.
- Expected: start times precise enough to subtract (ms, or at least 0.1 s).
- Got: `t=8m45s` for every row: at minute scale the list rounds to the second, so tests 0.8 s apart look simultaneous. Also, the bare terms matched hundreds of `console.warn` markers of those tests before the `test` markers.
- Workaround: `--search "name:test" --list --limit 0 --json` piped through a Python filter on `start`.
- Could show: millisecond start times in `--list` regardless of profile length (or a `--precise` flag), and a way to search only `test` markers by test path.


## `--search name:test` matches markers whose name is not `test` (review)

- Question: "which tests ran in this browser session before mine, in order?"
- Command: `profiler-cli thread markers --category Test --search "name:test" --list --limit 0 --session <s>` on a per-test profile.
- Expected: the `test` markers only (3 here).
- Got: 728 markers, starting with `Runnable` "AsyncPrecreateStringBundles ..." and `ExtensionSchemas` rows, and `--category Test` did not narrow it either.
- Workaround: `| grep ' test  '`.
- Could show: an exact marker-name filter (e.g. `--name test`), and `--category` applied together with `--search`.
