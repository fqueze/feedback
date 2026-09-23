## Marker search does not match `innerWindowID`

- Command: `profiler-cli thread markers --session <s> --search "4294967338" --list` on the WebExtensions main thread (value taken from `marker info --json` rawFields `innerWindowID`)
- Expected: the markers whose `innerWindowID` is that value (to see everything a given document's refresh driver did).
- Got: 0 markers, while `--search "waiting for paint" --group-by field:innerWindowID` groups all 1,079 of them under 4294967338.
- Workaround: `--group-by field:innerWindowID` on a narrower search, comparing against the `DocumentLoad` marker's `innerWindowID` from `marker info --json`.
