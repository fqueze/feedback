## Question: which page (URL) is the document behind a DOMEvent marker?

- Command: `profiler-cli marker info m-2191 --session test_bug112564.xhtml-1` on a `DOMEvent load - document` marker.
- Expected: the document's URL, or some command mapping the marker's `innerWindowID` to the profile's `pages` table (URL, browsing context / tabID, embedder).
- Got: `Event Target: document` and `innerWindowID: 30` under "Other payload fields (no schema)"; no command exposes the `pages` table (`profile info --all`, `profile meta` do not list it).
- Workaround: downloaded the profile JSON separately and read `pages[]` with a Python script to learn that innerWindowID 30 is `about:newtab` (tab 10), 37 is the test window. Needed in each of 4 profiles.
- What would have answered it: `marker info` resolving `innerWindowID` to its page URL, or a `profile pages` command listing innerWindowID -> URL, tabID, embedderInnerWindowID. `--search` on DOMEvent could also match the page URL.

## Question: which DOMEvents belong to one window (innerWindowID)? (review-test_bug112564.xhtml)

- Command: `profiler-cli thread markers --search "innerWindowID:30" --list --limit 0 --session review-test_bug112564.xhtml-1`
- Expected: the `DOMEvent` markers of innerWindowID 30 (about:newtab), for example to check that the page has only one `load`.
- Got: `No markers match the specified filters`, although `marker info --json` shows `innerWindowID: 30` under `rawFields` on those markers. `--search` does not look at `rawFields`. The first entry above (no innerWindowID -> URL mapping) was hit again in the review of all 4 profiles.
- Workaround: downloaded each profile JSON and listed `DOMEvent` markers with `data.innerWindowID` joined to `pages[]` in Python.
- What would have answered it: `--search innerWindowID:N` (or `page:<url substring>`) matching the marker's inner window.

## `profile markers --list` is rejected (review-test_bug112564.xhtml)

- Command: `profiler-cli profile markers --search "eventType:pageshow" --list --limit 0 --session review-test_bug112564.xhtml-1`
- Expected: the guide describes `profile markers` as "same rows as `thread markers --list`", so `--list` should be accepted or ignored.
- Got: `error: unknown option '--list'`.
- Workaround: dropped `--list`.
