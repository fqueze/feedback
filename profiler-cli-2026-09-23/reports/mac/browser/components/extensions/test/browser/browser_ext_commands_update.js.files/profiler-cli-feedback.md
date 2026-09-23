## Which document a "RefreshDriverTick waiting for paint" belongs to

- Command: `profiler-cli thread markers --search "RefreshDriverTick waiting for paint" --list`
- Expected: some way to see the marker's innerWindowID (the only payload) in the list, or to group by it.
- Got: the list shows no fields for NoPayloadUserData markers; `--group-by field:innerWindowID` is not
  offered for them, and `--search innerWindowID:<n>` matched 0 markers although `marker info` shows
  `rawFields: innerWindowID`.
- Workaround: `--list --limit 0 --json` and counting `data.innerWindowID` in a script; `marker info --json`
  for the DocumentLoad marker to map the id to a URL.
- Correction to the entry above: `--group-by field:innerWindowID` does work on these markers (3856 / 1 / 1),
  so the script was not needed for the count. What remains: `--search innerWindowID:4294967338` matches 0
  markers, and `--list` shows no innerWindowID, so the first/last time of one window's markers still
  needed the JSON.
