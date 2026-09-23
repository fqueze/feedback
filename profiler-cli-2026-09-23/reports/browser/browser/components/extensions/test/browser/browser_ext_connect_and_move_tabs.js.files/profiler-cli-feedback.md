## Marker times past one minute lose their sub-second part

- Command: `profiler-cli thread markers --category Test --search browser_ext_connect_and_move_tabs --list --limit 0 --session <s>` (and `marker info m-16`)
- Expected: `t=75.691s`-style times, as shown for markers before 60 s (`t=9.640s`).
- Got: `t=1m16s` / `t=1m24s`, so "checking for open popups" and the vsync timeout 8 s later cannot be told apart from neighbours in the same second, nor a duration computed.
- Workaround: `marker info ... --json` and read `start`.

## `--search innerWindowID:<n>` matches nothing

- Command: `profiler-cli thread markers --search 'innerWindowID:4294967338' --list --limit 0 --json --session <s>` on the WebExtensions GeckoMain.
- Question: "every marker tied to this one window (a page-action popup.html)".
- Expected: the ~4,400 markers whose payload has that innerWindowID (`--group-by field:innerWindowID` does see them).
- Got: 0 markers.
- Workaround: `--list --limit 0 --json` over a zoom, then filter on `data.innerWindowID` in a script.
