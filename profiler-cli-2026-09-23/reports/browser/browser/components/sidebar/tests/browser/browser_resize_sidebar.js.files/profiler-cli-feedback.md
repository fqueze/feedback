## Marker list timestamps lose all sub-second resolution after one minute

- Command: `profiler-cli thread markers --search sidebar.visibility --list --limit 0 --session <s>` on a 1m27s profile.
- Expected: `t=80.273s` (or `1m20.273s`), as markers under one minute get (`t=28.588s`).
- Got: `t=1m20s` for every marker past 60 s, so a 25 ms pref-write sequence cannot be ordered against the test log.
- Workaround: `marker info <m> --json` per marker and read `start`.

## Question: "which of these style-dirtying changes came from function X?"

- Command: loop over `thread markers --search SetNeedStyleFlush --list` handles, `marker stack <m>` for each, grep for `toggleExpandOnHover`.
- Needed to find which DOM changes a given function made in a window; `--search` on markers matches name/payload, not the captured stack. Over a 12 s window with 12,727 SetNeedStyleFlush markers the loop was too slow to run.
- What would have answered it: a stack-frame filter on `thread markers` (e.g. `--stack-search toggleExpandOnHover`).

## (review) Loading a raw Taskcluster profile URL selects a content-process thread

- Command: `profiler-cli load 'https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/BMFOuIyvTPqzBQnP3eMo7g/runs/0/artifacts/public/test_info/profile_browser_resize_sidebar.js.json' --session <s>`
- Expected: the parent process GeckoMain (t-0) selected, where the test log and harness markers are.
- Got: `Selected thread: t-36 (GeckoMain, Privileged Content)`. Easy to miss; later `thread markers` queries silently return another process's markers.
- Workaround: `thread select t-0` after every load.

## (review) Question again: "which style-dirtying changes in this window came from function X?"

- Command: `thread markers --search SetNeedStyleFlush --list` in a zoom, then `marker info m-A..m-B --json` and a Python filter over `stack.frames` for `toggleExpandOnHover`, `setLauncherCollapsedWidth`, `handleVerticalTabsPrefChange`, `launcherDragActive`. Needed four times in one review (two profiles each).
- What would have answered it: a stack-frame filter on `thread markers` (same request as above), e.g. `--stack-search toggleExpandOnHover`.
