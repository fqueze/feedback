## `thread markers --list` rounds times to whole seconds past one minute

- Command: `profiler-cli thread markers --search "eventType:mousedown,eventType:mouseup,_onPressEvent,..." --list --limit 0 --session <s>` on a 5m39s profile.
- Question: in what order did a 1000 ms setTimeout callback and the next mousedown happen, and how far apart (they were 23 ms apart)?
- Expected: millisecond timestamps, e.g. `t=323.548s`.
- Got: `t=5m24s` for every row in that second, so the order and the gaps could not be read.
- Workaround: `--json` and a Python script printing `start`.

## DOMEvent rows with target `window` don't say which window

- Command: `profiler-cli thread markers --search "eventType:OverLink" --list --limit 0 --session <s>`
- Question: which browser window got each OverLink event? The test had three windows open.
- Got: `OverLink - window` for every row. The innerWindowID is only in `--json` (`data.innerWindowID`) and under `marker info`'s "Other payload fields".
- Could show: the innerWindowID (or the window's URL/title) next to `window` in the list label.
- Workaround: `--json` and a script grouping by `data.innerWindowID`.

## The test log is swamped by multi-line console messages

- Command: `profiler-cli thread markers --category Test --search browser_recentlyclosed_firefoxview.js --list --limit 0 --session <s>`
- Question: the test's own log (TEST-*, INFO).
- Got: 1,253 markers but 44,233 lines, because `Console message:` INFO markers print their full JS stacks inline.
- Could show: console-message markers on one line, or an option to drop them (`-name:` exclusion does not help because they share the INFO name).
- Workaround: `grep -E '^  m-' | grep -v 'Console message'` on the dumped file.

## IPC marker raw time fields are not in profile time (review)

- Command: `profiler-cli marker info m-20 --json --session <s>` on an `IPCIn PBrowser::Msg_SetLinkStatus` marker.
- Question: was this message sent before or after a `TabSelect` at 317012.6, and when was it handled?
- Got: `start` 316980.4 / `end` 317058.9, but `rawFields` `startTime` 317005.7, `sendStartTime` 317005.7, `endTime` 317084.2. All the raw fields sit 25.2 ms later, which is the profile's zero offset. Read next to the other markers' times, the raw ones reverse the order, and the report under review quoted them as profile times.
- Could show: raw time fields shifted to profile time, or labelled as unshifted.
- Workaround: compare only `start`/`end`.
