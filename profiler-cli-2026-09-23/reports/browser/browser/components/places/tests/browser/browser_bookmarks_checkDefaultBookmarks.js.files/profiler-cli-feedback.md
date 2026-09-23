## `thread markers --list` timestamps too coarse for a short test

- Command: `profiler-cli thread markers --category Test --search browser_bookmarks_checkDefaultBookmarks --list --limit 0 --session browser-checkDefaultBookmarks-1`
- Expected: timestamps precise enough to order and zoom on the markers of a test that lasts 200 ms (e.g. `t=227.743s`).
- Got: every row `t=3m48s`; `marker info` also says `Time: 3m48s`. The whole test (Entering → TEST-UNEXPECTED-FAIL → Leaving) collapses to one value.
- Workaround: `--json` and read `start` from `flatMarkers`. Question it could have answered: "at what ms did each step of the test happen, to zoom on it".

## `load` selects a content-process thread by default on some profiles

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/Dv8wttkIRBmZKfx2YSXvwA/runs/0/artifacts/public/test_info/profile_browser_bookmarks_checkDefaultBookmarks.js.json --session browser-checkDefaultBookmarks-2`
- Expected: the parent process GeckoMain (t-0) selected, as it was for the same kind of profile from another job (RqreuoDg).
- Got: `t-19 (GeckoMain, Privileged Content)` selected; `thread markers --search TEST-UNEXPECTED` returned nothing and exited silently (no "0 markers" line with `--json` piped), which reads like "no failure in this profile".
- Workaround: `thread select t-0`. Cost: one wasted round of queries.
