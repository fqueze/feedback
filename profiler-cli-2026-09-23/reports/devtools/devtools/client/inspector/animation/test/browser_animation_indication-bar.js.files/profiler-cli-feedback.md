## Marker timestamps too coarse in `thread markers --list`

- Command: `profiler-cli thread markers --category Test --search browser_animation_indication-bar.js --list --limit 0 --session <s>`
- Expected: timestamps precise enough to order and cite the test's log (ms).
- Got: every row `t=1m2s` / `t=1m3s` in a 63 s profile, so the click (62.746 s), the a11y failure (62.835 s) and the passing asserts (62.895 s) all print the same time.
- Workaround: `--json` and read `flatMarkers[].start`.
- Question it could have answered: "at what ms did each step of the test log happen?" The list could print ms (e.g. `62.835s`) when the profile is longer than a minute.

## review-browser_animation_indication-bar.js (2026-09-22)

- Command: `profiler-cli load 'https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/H6RcZP1URlSmjnjo-6UreA/runs/0/artifacts/public/test_info/profile_browser_animation_indication-bar-2.js.json' --session S`, then `thread markers --category Test --search UNEXPECTED --list --limit 0`.
- Expected: the default selected thread to be the parent process GeckoMain (t-0), where the Test markers are, as it was for the first-run profile of the same job.
- Got: t-15 (GeckoMain, WebExtensions) selected, and "No markers match the specified filters." The thread is named in the header line, but an empty result looks like an answer.
- Workaround: `thread select t-0`. Selecting the parent process main thread by default, or saying "0 Test markers on this thread; t-0 has N" on an empty result, would avoid it.
