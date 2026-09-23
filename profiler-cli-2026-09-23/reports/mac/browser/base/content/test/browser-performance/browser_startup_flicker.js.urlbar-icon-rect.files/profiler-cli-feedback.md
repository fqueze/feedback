## Question: "which markers in this range have a stack through function X"

- Command: `profiler-cli thread markers --has-stack --list --limit 0 --json` inside a `zoom push`, then `profiler-cli marker info <150 handles> --json` in batches (2,260 markers, 16 calls, 9 MB of JSON), then a Python grep over `stack.frames[].nameWithLibrary` for `SearchModeSwitcher`, `getEngineIconURL`, `browser-test.js!nextTest`.
- Expected: one command listing the markers whose captured stack contains a frame matching a substring, e.g. `thread markers --stack-search SearchModeSwitcher --list`, with the matching frame and a few frames around it on each row.
- Got: `--search` on `thread markers` matches the name, category and payload only, and `thread functions --search SearchModeSwitcher` finds nothing because no sample landed in that code (10 ms sampling). The only way to find the one `ChromeUtils.importESModule` marker whose stack showed `SearchModeSwitcher#getSearchIcon -> getEngineIconURL` was the script above.
- What would have answered it: a stack-frame filter on `thread markers` (and on `marker info` ranges), since in CI profiles marker stacks are the only trace of short JS work.

## (review-browser_startup_flicker.js.urlbar-icon-rect) Question: "which markers in this window have a stack through function X" — again

- Command: `profiler-cli thread markers --has-stack --list --limit 0 --json` inside `zoom push 1.85,1.87` (and 5.07,5.1 and 3.185,3.3 on two other profiles), a regex over the JSON for `m-N` handles, `profiler-cli marker info <44–163 handles> --json`, then a Python grep over `stack.frames[].nameWithLibrary` for `browser-test.js!nextTest`, `lit.all.mjs`, `SearchModeSwitcher`.
- Expected: `thread markers --stack-search <substring>` (same as the entry above).
- Got: the same 3-step workaround, once per profile.
- What would have answered it: the stack-frame filter proposed above.

## (review-browser_startup_flicker.js.urlbar-icon-rect) Loading a bare Taskcluster artifact URL selects a content process thread

- Command: `profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/dMXVuG2rRSCO1u20mGsufw/runs/0/artifacts/public/test_info/profile_browser_startup_flicker-2.js.json" --session …`, then `thread markers --search blob:system` and `profile-link.py --marker m-4`.
- Expected: the parent process main thread selected, as it is when the same profile is loaded through a `profiler.firefox.com/from-url/…&thread=0` link.
- Got: `t-11 (GeckoMain, Privileged Content)` selected. The search returned a single `search-glass.svg` paint from that process, with no error, and the link came out with `thread=b`. I only noticed because the link did not say `thread=0`.
- Workaround: `thread select t-0` after every bare-URL load.
