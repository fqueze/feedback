## Question: in what order, to the millisecond, did these markers happen?

- Command: `profiler-cli thread markers --category Test --search browser_sidebar_splitter_keyboard.js --list --limit 0 --session <s>` (and the same with `--search DeferredTask,"setTimeout callback"`)
- Expected: a start time precise enough to order markers that are milliseconds apart.
- Got: every row shows `t=2m55s`. The ~60 markers covering 10 s all show the same one or two values, so there is no way to tell whether a DeferredTask ran before or after a given test log line.
- Workaround: `--json` piped through a python script printing `flatMarkers[].start`. Needed for every list in this investigation.
- What the output could show: a start time at ms precision (e.g. `175384.294ms`, or `2m55.384s`), at least once the view's range is under a minute.

## Question: which of these markers were set from a stack through function X?

- Command: `thread markers --search SetNeedStyleFlush --list`, then `marker stack m-N` in a shell loop over 234 handles, then grep.
- Expected: a way to filter markers by a frame in their captured stack, e.g. `--stack-search _updateSplitterAriaAttributes`.
- Got: `--search` matches only name/payload. It took 234 `marker stack` calls.
- Workaround: the shell loop above.

## Minor: `flatMarkers[].end` is missing for interval markers in `--list --json`

- Command: `thread markers --search DeferredTask --list --json`
- Expected: an `end` or `duration` field for interval markers such as `Runnable`, `DeferredTask`.
- Got: `m.get('end')` is None. Had to call `marker info` to get a duration.

## (review) Question: which idle-priority tasks ran in this range?

- Command: `thread markers --search "priority: Idle" --list --session <s>` (zoomed to 136 ms)
- Expected: the Runnable markers whose label reads `... - priority: Idle (0)`.
- Got: 0 markers, no warning. The colon made it a `field:value` search on `priority`, whose value is the number 0 (the name is in `priorityName`).
- Workaround: `--search name:Runnable --json`, then grep the labels for `Idle (0)`.
- What the output could show: a note when a `field:value` term matches nothing but the bare text would, or when the field holds a number.

## (review) Question: when did the splitter receive each keydown?

- Command: `thread markers --search "keydown - splitter" --list`
- Expected: the `DOMEvent` markers labelled `keydown - splitter@...`.
- Got: nothing, silently. `--search keydown,command` then grepping for `splitter` worked.
- What the output could show: say how the search term was parsed when it matches nothing.

## (review) Minor: loading a raw test_info profile URL selects a content GeckoMain

- Command: `profiler-cli load https://firefox-ci-tc.services.mozilla.com/.../profile_<test>.js.json`
- Expected: the parent process GeckoMain (t-0), where the test log is, as when loading the profiler.firefox.com link.
- Got: `t-19 (GeckoMain, Privileged Content)` selected; every search on it came back empty until `thread select t-0`.
