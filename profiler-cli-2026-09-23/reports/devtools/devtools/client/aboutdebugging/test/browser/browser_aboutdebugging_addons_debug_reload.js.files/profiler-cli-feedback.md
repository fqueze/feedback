## Marker times past one minute lose sub-second precision

- Question: in what order did `sidebar-hide`, `Removing tab.` and `Leaving test` happen, all within the same second (t=81.5-81.7s)?
- Command: `profiler-cli thread markers --session <s> --search "sidebar-hide,Removing tab" --list --limit 0` and `profiler-cli marker info m-281 m-328 --session <s>`
- Expected: `t=1m21.509s`-style times, as the list gives below one minute (`t=8.123s`).
- Got: `t=1m22s` for every one of them, so their order within the second is lost; `marker info` prints `Time: 1m22s - 1m22s (121.63μs)`.
- Workaround: `marker info ... --json` and read `start`.

## Which DOMWindow lifetime marker is the window a leak FAIL marker blames

- Question: in a resource-usage profile, which `DOMWindow` marker (pid, serial, lifetime) is the window behind `FAIL leaked window until shutdown [url = about:blank]`?
- Command: `profiler-cli thread markers --session <s> --search "name:DOMWindow" --list --limit 0 --json`, then a script keeping the marker whose `start` equals the FAIL marker's `start` (92130.071).
- Expected: the FAIL marker naming the window it is about (pid and serial, as the log's `windows(s) leaked: [pid = 1567] [serial = 184]` does), or `marker info` on it pointing at the matching `DOMWindow` marker.
- Got: nothing ties them but an identical start time; the zoomed `--list` also shows every marker overlapping the range, so a 1 s zoom lists windows alive since startup first.
- Workaround: the script above. Also, the `DOMWindow` marker for serial 184 printed `outer = 0` (the destruction line's value), so the inner-to-outer link (which docshell it belonged to) had to come from the log.

## (review) Searching the text a marker list prints does not find the marker

- Question: which `DOMWindow` marker is window serial 184?
- Command: `profiler-cli thread markers --session <s> --search 'serial = 184' --list --limit 0`
- Expected: the row the list prints as `DOMWINDOW 7f76886ed100 [Parent 1,567: Main Thread] serial = 184 outer = 0 about:blank`.
- Got: only an unrelated `TEST-INFO ... windows(s) leaked: [pid = 1567] [serial = 184]` row. The printed label is not searched, and no single payload value is `serial = 184`.
- Workaround: `--search name:DOMWindow --json` and a script matching the `label`. `serial:184` would probably have worked, but nothing in the printed row says the label is not searchable or which field holds 184.
