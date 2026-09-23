## Question: "which timers fired late, and by how much?"

- Command: `profiler-cli thread markers --search "name:setTimeout callback" --list --limit 0 --json`, then one `profiler-cli marker info m-N` per marker (109 calls) to read "Captured at", which is when the timer was set.
- Expected: the list, or `--json`, would give for each `setTimeout callback` marker the time its stack was captured, meaning when the timer was set, so set-to-fire delays could be read directly. `innerWindowID` is only in `data` in the JSON, not in the text list either.
- Got: the flat list and `flatMarkers[]` have neither the stack capture time nor the window id in text form, so I had to write a script looping over `marker info`.
- What would have answered it: a column with the stack capture time, or a delay (start minus capture), for markers whose stack is captured at a different time, plus the innerWindowID in the text listing. Better still, `thread markers --search "name:setTimeout callback" --sort delay`.

## Question: "where is the async stack of this uncaught rejection?" (review)

- Command: `profiler-cli marker info m-2` and `marker stack m-2` on the resource-usage profile's `JavaScript error` marker for the TypeError.
- Expected: the JS stack of the error, or a pointer to the marker that has it.
- Got: a single frame, `autocomplete-popup.js!null`. The async stack is on the neighbouring `FAIL` marker (m-1). The report under review had cited the wrong one of the two.
- What would have answered it: `marker info` on a `JavaScript error` marker could name the `FAIL`/`TEST-UNEXPECTED-FAIL` marker with the same message and its stack depth. Or, for a one-frame stack, print its frame name rather than `null`.

## Loading a profiler.firefox.com link (as review-brief.md says) breaks profile-link.py

- Command: `profiler-cli load "https://profiler.firefox.com/from-url/…"`, then `python3 profile-link.py --session … --marker m-N`
- Expected: a link.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself". I had to reload both profiles. Loading the raw URL then selected t-130, a content process, rather than t-0.
- Workaround: load the Taskcluster URL and `thread select t-0`. Either profile-link.py could accept a session loaded from a from-url link, or review-brief.md could say to load the raw URL.
