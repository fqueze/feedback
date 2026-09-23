## Marker times are printed to the second in a long profile

- Command: `profiler-cli thread markers --session <id> --search browser_theme_switching --list --limit 0` on a 20-minute resource-usage profile (zoomed to the 2.9 s test marker).
- Question: how many milliseconds passed between the INFO "Check that disabling HCM…" marker and the next PASS, i.e. how long two `pushPref` calls took.
- Expected: times at a resolution that matches the zoomed range (ms inside a 3 s zoom).
- Got: every row shows `t=7m36s`, even when zoomed to the test's own marker.
- Workaround: `profiler-cli marker info m-.. m-.. --json` and a Python one-liner reading `.markers[].start`.

## Which document a RefreshDriverTick is for

- Command: `profiler-cli thread markers --session <id> --search "name:RefreshDriverTick" --list`
- Question: which refresh driver (browser.xhtml or about:devtools-toolbox) each tick belongs to, to see whether the two drivers tick in the same vsync and in which order.
- Expected: the list row to show the marker's innerWindowID, ideally resolved to the page URL.
- Got: only the tick reasons; innerWindowID is only in `marker info` ("Other payload fields") or in `--json` `.data.innerWindowID`, and nothing maps an innerWindowID to a URL. I mapped them by hand from the `initial timer start <url>` markers, which carry the same field.
- Workaround: `--list --json` and a Python script.
