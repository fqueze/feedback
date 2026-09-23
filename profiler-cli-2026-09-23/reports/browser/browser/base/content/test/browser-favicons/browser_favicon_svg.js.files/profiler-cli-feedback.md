## Question: did marker A end before marker B started? (sub-ms ordering)

- Command: `profiler-cli thread markers --list` and `profiler-cli marker info m-88 m-360`
- Question: did the `load` DOMEvent (52.470s, 2.308ms) end before the next `RefreshDriverTick` (52.472s) began? Both commands round start and end to the millisecond (`t=52.472s`, `Time: 52.470s - 52.472s`), so I could not tell.
- Workaround: `marker info ... --json` and a python one-liner to print `start`/`end` (52469.916 to 52472.224, and 52472.329).
- What would have answered it: `marker info` printing start and end with µs precision, or at least enough digits to order markers whose rounded times collide.

## Bare `--search` terms match unexpected markers

- Command: `thread markers --search "CSS transition,Image Load,load - html:img,TEST-UNEXPECTED,Load ,RefreshDriverTick,TabOpen" --list`
- Expected: only markers of those names or those DOMEvent payloads.
- Got: dozens of `ThemeChanged` and `SetNeedStyleFlush` markers. Some term (probably `Load ` or `TabOpen`) matched something in their payload or stack, and the output does not say which field matched.
- Workaround: add `-name:ThemeChanged,-name:SetNeedStyleFlush`.

## Question (review): link a new observation in a profile opened from a report's link

- Command: `profiler-cli load "https://profiler.firefox.com/from-url/<encoded taskcluster URL>/marker-table/?marker=277529&thread=0&v=17" --session S`, then `profile-link.py --session S --marker m-472`
- Expected: a link. The review brief says to open profiles from the report's links.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself". I had to load the same 21 MB profile a second time from the raw URL.
- What would have answered it: record the underlying `from-url` artifact URL when a profiler.firefox.com link is loaded, so that links can be built from that session.

## Loading a raw per-test profile selects the WebExtensions main thread

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/YxfbX6IESXKosq2IK7UL3g/runs/0/artifacts/public/test_info/profile_browser_favicon_svg.js.json --session S`
- Expected: `t-0 (GeckoMain, Parent Process)` selected, where the test log is.
- Got: `t-42 (GeckoMain, WebExtensions)` selected. `thread markers --category Test` returned nothing until I ran `thread select t-0`.
