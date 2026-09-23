## Question: at what millisecond did each test-log line happen, to line it up with DOMEvent markers?

- Command: `profiler-cli thread markers --session <s> --category Test --search browser_touch_all_events.js --list --limit 0`
- Expected: times precise enough to order markers against each other (ms), e.g. `t=104.602s`.
- Got: past one minute into the profile, every row reads `t=1m45s`, `t=1m47s`... — a whole second of resolution. Inside a 60 ms zoom every DOMEvent row showed the same `t=1m47s`, so the order and gap between `mousedown`, `mouseup`, `click` and the `TEST-UNEXPECTED-FAIL` could not be read.
- Workaround: `--json | jq '.flatMarkers[] | "\(.handle) \(.start) \(.label)"'`. The default output could print `t=104.602s` (or `1m44.602s`) as it does under a minute.

## Same question again, in review (review-browser_touch_all_events.js.a11y-checks)

- Command: `profiler-cli thread markers --session <s> --search "eventType:click" --list --limit 0`, then the same with `--category Test --search "tagName: browser"`.
- Expected: ms timestamps, to check that each of 11 `TEST-UNEXPECTED-FAIL` falls inside one of 11 `click - browser` markers.
- Got: `t=1m45s` for all of them again; no duration end either.
- Workaround: a shell loop of `marker info m-N --json` over 22 handles to print `start`/`end`. Same fix as above would have answered it.
