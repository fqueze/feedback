## Question: "which innerWindowID do these 25,436 `RefreshDriverTick waiting for paint` markers belong to?" (review)

- Command: `profiler-cli thread markers --session S --search "waiting for paint" --group-by innerWindowID` (also `--search innerWindowID:4294967338`)
- Expected: one group per inner window, since innerWindowID is how a marker is tied to a document.
- Got: `(no value): 25436 markers`, and 0 matches for the search: innerWindowID is in `rawFields`, which neither `--group-by` nor `field:value` sees. Workaround: `marker info m-A..m-B --json` in 100 chunks of 256 (the range cap), then a Python tally.
- Could show: innerWindowID as a groupable/searchable key, or its URL resolved from the page list.

## profile-link.py refuses a session loaded from a profiler.firefox.com link

- Command: `profiler-cli load 'https://profiler.firefox.com/from-url/.../marker-table/?marker=1260851&thread=0&v=17' --session S`, then `profile-link.py --session S --thread t-0 --range 9.5,19.2 --panel marker-table --search popupshowing`
- Expected: a link, since review-brief.md says to check a report by loading its links.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself". A reviewer that wants to link a new observation must reload the same profile from the raw URL (another 0.6 GB).
- Could show: extract the raw URL from the `from-url/` path of a profiler link.
