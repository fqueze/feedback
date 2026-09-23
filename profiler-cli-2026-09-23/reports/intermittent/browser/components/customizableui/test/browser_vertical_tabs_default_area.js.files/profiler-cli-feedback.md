
## review-browser_vertical_tabs_default_area.js (2026-09-22)

**Question: "in which of these 35 `Preference Write` markers of `browser.uiCustomization.state` does id X appear, and under which JSON key?"**

- Command: `profiler-cli thread markers --search uiCustomization.state --list --limit 0 --session <s>`
- Expected: a way to see the part of a long payload value that matched, or to search inside it (e.g. `--search prefValue:test-vertical-default-area-button` returning the rows, with the list column showing the match in context instead of the first ~120 characters).
- Got: each row's payload truncated to its start (`{"placements":{"widget-overflow-fixed-list":[],...`), identical for all 35 rows, so the list cannot tell them apart.
- Workaround: `profiler-cli marker info m-38..m-72 --json` and a Python script parsing each `prefValue` as JSON. A `--search` term alone would say which rows contain the id, not whether it is under `placements` or `seen`, so a "show match in context" snippet would have been enough for most of it.

**`profile-link.py` refuses a session loaded from a profiler.firefox.com link** (review-browser_vertical_tabs_default_area.js, 2026-09-22)

- Command: `profiler-cli load '<profiler.firefox.com/from-url/...?marker=61526&thread=0>' --session S`, then `profile-link.py --session S --marker m-41`.
- Expected: a link (the review brief says to check a report by loading its links, and the from-url link wraps the same Taskcluster URL).
- Got: `A link needs the raw artifact URL: load the Taskcluster URL itself, not a local copy or a profiler link.`
- Workaround: stop the session and reload the decoded Taskcluster URL (another ~0.6 GB load, and thread selection resets to t-15, so `thread select t-0` first). `profile-link.py` could decode the `from-url` part itself.
