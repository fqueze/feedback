## Question: "which document does this ticking refresh driver belong to, and what else happened in it?"

- Command: `profiler-cli thread markers --session <s> --search "innerWindowID:4294967338" --limit 3` (thread t-30, WebExtensions process), and also with the bare term `--search 4294967338`.
- Expected: the markers carrying that innerWindowID (19,422 `RefreshDriverTick waiting for paint`, plus the `DocumentLoad` and `DOMEvent` markers of the same document).
- Got: "0 markers ... No markers match the specified filters." Yet `--group-by field:innerWindowID` on the same markers works and shows 4294967338, and `marker info --json` lists it under `rawFields`. So `--search` does not look at raw (schema-less) payload fields.
- Workaround: zoom into the time the ticks start, list `name:DocumentLoad` markers, and `marker info --json` each to read `rawFields.innerWindowID`, then match by hand.
- What would have answered it: `--search innerWindowID:<id>` matching raw fields; or `DocumentLoad`/tick markers printing the document URL for their innerWindowID (the profile's pages table has it).
