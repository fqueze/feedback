## Question: which document (inner window) does each marker in a content process belong to?

A parent page and its same-process subframe (`<embed>`) share one GeckoMain thread. To tell the
parent's `Styles`/`Reflow`/`DOMEvent load` from the subdocument's, I needed each marker's
`innerWindowID`.

- `profiler-cli thread markers --list --category Layout --session <s>`: the list does not
  show `innerWindowID`, so the two documents' markers cannot be told apart.
- `profiler-cli thread markers --list --search "innerWindowID:109521666052"` and
  `--search 109521666052` both return "0 markers", although `marker info` shows that
  value under "Other payload fields (no schema): innerWindowID".
- `marker info` shows it, but only one marker at a time.

What I did instead: ran `marker info m-a m-b ... --json` and a Python script that prints `rawFields.innerWindowID`
next to each handle.

What would have answered it: an inner-window or document column in `--list`, ideally
resolved to the document URL (the profile's `pages` table has it), or `--search` matching
schema-less payload fields such as `innerWindowID`.

## Question (review): which session marker is the one a link's `marker=N` points at? (review-browser_pdfjs_embed_fallback.js)

- `profiler-cli marker info m-90` (text output) and `thread markers --list` do not show `markerIndex`. Checking that a report's `marker=N` links point at the quoted markers took `marker info m-a m-b ... --json` and a Python script printing `markerIndex` next to each handle.
- What would have answered it: `markerIndex` in the `marker info` text output, or `marker info --index N`.

## profile-link.py refuses a session loaded from a profiler link (review-browser_pdfjs_embed_fallback.js)

- Command: `PROFILER_CLI_SESSION_OWNER=... python3 profile-link.py --session review-browser_pdfjs_embed_fallback.js-1 --marker m-701`. The session had been loaded with `profiler-cli load 'https://profiler.firefox.com/from-url/<tc url>/marker-table/?marker=6875&thread=m&v=17'`, which is what review-brief.md says to do.
- Expected: a link. Got: "A link needs the raw artifact URL: load the Taskcluster URL itself".
- Workaround: stopped the sessions and reloaded both profiles from their raw Taskcluster URLs, just to build two new links. That cost two extra profile loads.
- Fix: have profile-link.py pull the artifact URL out of a `from-url/` link.
