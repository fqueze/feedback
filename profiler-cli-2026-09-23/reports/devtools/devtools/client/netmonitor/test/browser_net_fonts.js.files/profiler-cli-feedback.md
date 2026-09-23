## `zoom push` takes seconds, while `thread markers --json` gives `start` in milliseconds

- Command: `profiler-cli zoom push 417352,417393 --session browser_net_fonts.js-1`, with the two values copied from `flatMarkers[].start` of `thread markers --json`.
- Expected: a 41 ms window, or an error saying the range is past the end of the profile (8m26s).
- Got: a 41 s window (`View: ts>10N→ts>10o (41s)`), accepted without warning. The empty call tree that followed looked like "nothing ran there".
- Workaround: divide by 1000 (`zoom push 417.352,417.393`).
- Suggestion: accept an `ms` suffix, warn when a range starts past the profile's end, or print `start` in the JSON in the same unit that `zoom push` takes.

## Question: "when did this JS function run?" (sample timestamps)

- I wanted to know whether `onNetworkResourceAvailable` ran before or after a given marker. `thread samples*` / `thread functions` only aggregate, so I ran them over successively narrower `zoom push` windows. At 10 ms sampling the handler never showed up.
- What answered it: the `DevTools:RDP Front` / `DevTools:RDP Actor` markers, found only by listing every marker in a 126 ms window. A way to list the sample times of the samples matching `--search`/`--includes-function`, or a hint in `guide` that DevTools RDP packets are markers, would have saved several calls.

## Question: "in what order, to the millisecond, did these markers happen?" (review-browser_net_fonts.js)

- Command: `profiler-cli thread markers --session review-browser_net_fonts.js-1 --search "resources-available,batching,Clearing,Starting test" --list --limit 0`, after `zoom push 416.9,417.5` (a 600 ms view).
- Expected: start times precise enough to order markers within that view, e.g. `6m57.281s`.
- Got: every row says `t=6m57s`, so a 50 ms race cannot be read from the list.
- Workaround: `marker info m-48..m-73 --json` piped through a Python script that formats `start`.
- Suggestion: scale the printed precision to the view duration (ms when the view is under ~10 s), or add a `--precise-times` flag to `--list`.
