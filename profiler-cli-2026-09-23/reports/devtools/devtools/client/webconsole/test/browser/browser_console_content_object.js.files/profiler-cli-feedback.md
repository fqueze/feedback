## `zoom push 23m23s,24m55s` silently zooms to 1 s

- Command: `profiler-cli zoom push 23m23s,24m55s --session browser_console_content_object.js-1` (on a 24m55s profile whose marker list prints times as `t=23m23s`)
- Expected: a zoom to that range, or an error saying the minute format is not accepted.
- Got: `View: ts-1→ts-2 (1s)` with no warning; the following marker search returned nothing, which reads as "no markers there".
- Workaround: pass seconds (`1403.5,1495`).

## review: which marker index does a handle have (to check a `marker=N` link)

- Command: `profiler-cli marker info m-1 --session review-browser_console_content_object.js-1`
- Question: is this the marker the report's `marker=70218` link points at?
- Expected: the index in the default output, e.g. `Marker m-1 (index 70218)`.
- Got: only in `--json` (`markerIndex`), so every link check needed a script over the JSON.
- Workaround: `marker info m-N --json | python3 -c '...["markerIndex"]'`.

## review: `profile-link.py` refuses a session loaded from a profiler link

- Command: `profiler-cli load 'https://profiler.firefox.com/from-url/…/marker-table/?marker=73887&thread=0&v=17' --session review-…-3`, then `python3 profile-link.py --session review-…-3 --marker m-260`
- Expected: a link. The review brief says to load a report's links with `profiler-cli load <link>`.
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself`.
- Workaround: reloaded both profiles from their raw Taskcluster URLs to build two links. Either `profile-link.py` could take the artifact URL from the `from-url` link, or the review brief could say to load the raw URL.

## review: the tests of one manifest, in order, with their status

- Command: `profiler-cli thread markers --search name:test --list --limit 0`
- Question: which tests ran in this browser session, in which order, and with which status?
- Got: `name:` also matches Text-marker payloads, so the list mixes in many unrelated markers. I needed `awk '$2=="test"'` to keep only the `test` markers.
- Could show: a way to filter on the marker name alone, or a `--type Test` filter.
