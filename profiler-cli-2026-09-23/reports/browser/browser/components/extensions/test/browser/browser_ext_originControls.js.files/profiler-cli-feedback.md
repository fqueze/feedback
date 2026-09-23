## Question: which markers have a stack containing function X
- Command: `for m in $(profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0 --session S | grep -o "m-[0-9]*"); do profiler-cli marker stack $m --session S | ...; done | grep ext-browserAction`
- Expected: find the SetNeedStyleFlush marker whose stack goes through `ext-browserAction.js!callback` (the rAF that sets the attribute), e.g. `thread markers --search SetNeedStyleFlush --stack-search ext-browserAction`.
- Got: `--search` matches marker name/payload only, so each marker's stack had to be fetched on its own (about 50 calls per window).
- Workaround: the loop above.

## Question: which marker does a profiler.firefox.com link's `marker=N` point at (review, browser-review-browser_ext_originControls.js)
- Command: `profiler-cli marker info m-128 --json --session S | python3 -c '...print(d["markerIndex"])'`, once per linked marker (17 links over 4 profiles).
- Expected: the default `marker info` output (or `thread markers --list`) to show the marker index used in profiler URLs, or `marker info --index 56383` to resolve a link's `marker=N` directly.
- Got: the index only appears in `--json`, so checking each link needed finding the marker by search, then a script over its JSON.
- Workaround: the loop above.
