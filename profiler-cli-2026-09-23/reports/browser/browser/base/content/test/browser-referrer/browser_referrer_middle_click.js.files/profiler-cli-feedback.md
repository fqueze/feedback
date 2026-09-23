## Searching for a marker by the label the list prints (review)

- Command: `profiler-cli thread markers --session <s> --search "mousedown - document" --list`
  (also `profiler-cli profile markers --search "mousedown - "`)
- Expected: the `DOMEvent` marker the list itself prints as `mousedown - document`.
- Got: `No markers match the specified filters.` The label is built from `eventType` and
  `target`, and neither field holds the joined string, so the text shown is not searchable.
- Workaround: `--search mousedown`, then read the target off each row.

## profile-link.py cannot be used on a session loaded from a profiler link (review)

- Command: `python3 profile-link.py --session <s> --marker m-71`, on a session loaded with
  `profiler-cli load 'https://profiler.firefox.com/from-url/...'`, as the review brief says to.
- Expected: a link, to compare its `thread=` with the report's.
- Got: "A link needs the raw artifact URL: load the Taskcluster URL itself".
- Workaround: decoded the `thread=` parameter by hand (`x7` = 39) and matched it against
  `thread list --json` `threadIndex`. Either accept profiler links, or have the review brief
  say how to check a link's thread.
