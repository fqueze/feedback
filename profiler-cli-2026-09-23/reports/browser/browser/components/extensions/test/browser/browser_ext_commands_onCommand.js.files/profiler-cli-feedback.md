## Which document a marker belongs to (innerWindowID -> URL)

- Question: which document's refresh driver emits the `RefreshDriverTick waiting for paint` markers (they carry only an `innerWindowID` raw field).
- Commands: `profiler-cli marker info m-2002 --json` shows `rawFields: [{key: innerWindowID, value: 4294967331}]` but no URL; `thread markers --search innerWindowID:4294967331` and `--search 4294967331` both match 0 markers.
- Expected: `marker info` resolving the innerWindowID through the profile's `pages` table to its URL (the front-end does this), and/or a filter by page/URL.
- Workaround: downloaded the profile and ran `jq '.pages[] | select(.innerWindowID==4294967331)'` -> `moz-extension://.../popup.html`.
