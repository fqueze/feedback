## Marker times past one minute lose sub-second precision in `--list`

- Command: `profiler-cli thread markers --session browser_dbg-reloading.js-1 --category Test --search browser_dbg-reloading --list --limit 0`
- Expected: timestamps precise enough to order the test's steps (e.g. `t=82.847s`, `t=83.550s`, `t=83.743s`).
- Got: `t=1m23s`, `t=1m24s`, `t=1m24s` — the pause, the resume and the failure a few hundred ms apart are indistinguishable; the same in `marker info` (`Time: 1m24s`).
- Workaround: `profiler-cli marker info m-42 m-60 m-16 --json` and read `start`.

## (review) `profile-link.py` refuses a session loaded from a report's profiler link

- Command: `profiler-cli load '<the report's https://profiler.firefox.com/from-url/...&thread=0&v=17 link>' --session review-browser_dbg-reloading.js-1`, then `profile-link.py --session review-browser_dbg-reloading.js-1 --marker m-37`
- Expected: a link, since the review brief says to check a report by loading its link, and the from-url link carries the Taskcluster URL.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself", so adding an observation to a report meant stopping the session and loading the profile a second time.
- Workaround: `profiler-cli stop`, then `profiler-cli load <raw Taskcluster URL>` and re-find every marker handle.

## (review) Loading the raw artifact URL selects a content-process thread

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/.../profile_browser_dbg-reloading.js.json --session review-browser_dbg-reloading.js-2`, then `thread markers --search "Finished waiting for state change: paused" --list`
- Expected: the parent process main thread, where the test log is, as when loading the profiler link.
- Got: t-29 (GeckoMain, https://example.com) selected, so the search found nothing and nothing said why.
- Workaround: `profiler-cli thread select t-0`.
