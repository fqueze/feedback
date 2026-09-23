## A failed `load` leaves the session id taken

- Command: `profiler-cli load https://.../artifacts/public/profile_resource-usage.json --session test_DownloadCore.js.xattr-wherefroms-5` (wrong URL, 404), then the same `load` with the right URL.
- Expected: the second load to start the session, since the first never loaded anything.
- Got: `Error: Session test_DownloadCore.js.xattr-wherefroms-5 is already running. Stop it first or choose a different session id.`, and every query on it replied with the first load's `404 Not Found`.
- Workaround: `profiler-cli stop <id>`, then load again.

## `marker info` prints whole seconds on a long profile

- Command: `profiler-cli marker info m-2` on a 22-minute resource-usage profile.
- Expected: the start and end to the millisecond, as `--json` has them (`start: 284151.5, end: 285009.5`).
- Got: `Time: 4m44s - 4m45s (858ms)`; lining it up with the per-test profile or the job log needed `--json`.
