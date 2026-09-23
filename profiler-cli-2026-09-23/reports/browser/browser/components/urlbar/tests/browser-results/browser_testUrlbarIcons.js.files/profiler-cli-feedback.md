## `thread markers --list` prints `t=1m` for every marker once the profile is over a minute long

- Command: `profiler-cli thread markers --category Test --search browser_testUrlbarIcons --list --limit 0 --session <s>` (profile 1m2s long, test at 59.6 s).
- Expected: timestamps precise enough to order events and measure gaps (ms, e.g. `59640.810ms` or `59.641s`).
- Got: every row shows `t=1m` (and `t=1m1s`), so the test log, DOMEvents and ticks within the same 15 ms could not be ordered or compared.
- Workaround: `--json` and print `flatMarkers[].start` with a script. The question was "how many ms between the pref write, the failing assertion, and the next refresh tick of this window"; the default output could have shown ms-resolution start times, or times relative to the zoom start.

## `thread markers --list` does not show `innerWindowID`

- Command: `profiler-cli thread markers --search "RefreshDriverTick,nativethemechange" --list --session <s>`
- Question: "which window's refresh driver ticked when" — several windows tick on the parent main thread.
- Got: rows look identical across windows; only `marker info` (one call per marker) or `--json` `data.innerWindowID` tells them apart.
- Could have shown: the innerWindowID (or window URL) as a column when markers in the list carry different ones.

## (review) `t=1m` in `--list` hit again

- Command: `profiler-cli thread markers --session <s> --list --limit 0 --search "-name:Runnable,-name:IPCIn,..."` inside a 16 ms zoom at 59.6 s.
- Question: "in what order did the pref write, the ticks per window and the assertion happen, and how many ms apart". Same as the entry above: I needed `--json` plus a script to get ms times and `innerWindowID`. Also, some `flatMarkers` entries have no `data` key at all, so the obvious `m['data']` script raises KeyError.
