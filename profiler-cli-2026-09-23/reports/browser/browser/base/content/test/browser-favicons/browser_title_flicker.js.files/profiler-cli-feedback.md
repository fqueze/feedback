## Marker times lose precision past one minute

- Question: when exactly (to the ms) did the TEST-UNEXPECTED-FAIL happen, 101 s into a profile?
- Command: `profiler-cli thread markers --category Test --search browser_title_flicker --list --limit 0 --session S`, then `profiler-cli marker info m-10 m-16 --session S`
- Expected: `t=100.767s` (or `1m40.767s`), as under one minute where it prints `t=3.314s`.
- Got: `t=1m41s` for every marker of the test, in both `--list` and `marker info` (`Time: 1m41s`), so the order of events within the test and a `zoom push` range cannot be read from the output.
- Workaround: `marker info ... --json` and a script to read `.start`.

## `load <Taskcluster URL>` selects a WebExtensions thread, not the parent main thread (review)

- Question: the parent main thread markers of a per-test mochitest profile.
- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/S3Sb0uEPTLKVOcO3VOKJ0Q/runs/0/artifacts/public/test_info/profile_browser_title_flicker.js.json --session S`, then `thread markers --search ...`
- Expected: `t-0 (GeckoMain, Parent Process)` selected, the thread with the most CPU and the test's log.
- Got: `Selected thread: t-18 (GeckoMain, WebExtensions)`; the first query returned nothing, with no hint that the thread was the reason.
- Workaround: `thread select t-0`.
