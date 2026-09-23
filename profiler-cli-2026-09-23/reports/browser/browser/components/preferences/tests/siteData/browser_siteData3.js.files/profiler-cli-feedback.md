## Question: "when exactly did this marker start and end"

- Command: `profiler-cli marker info m-33 m-34 m-40 --session <s>`
- Expected: start/end to the millisecond, as the markers are 1-10s long in a 2m19s profile.
- Got: `Time: 2m9s - 2m18s (8.839s)`: whole seconds only once the profile passes one minute, so ordering markers that start in the same second (the two `sitedata-settings-init` waits, the `load`/`unload` waits) is impossible from the text.
- Workaround: `--json` and a Python one-liner over `.markers[].start/end`.

## Question: "which page is innerWindowID 310"

- Command: `profiler-cli marker info m-45 --session <s>` shows `innerWindowID: 310` in the payload; nothing in `profile info`, `profile meta` or `thread list` maps it to a URL.
- What the output could have shown: the page URL next to the innerWindowID (the profile's `pages` array has it: `about:preferences#privacy`).
- Workaround: downloaded the 40MB profile separately with curl and read `pages` with Python.

## `load` left a WebExtensions thread selected

- Command: `profiler-cli load <taskcluster url of profile_browser_siteData2.js.json> --session <s>`, then `thread markers --min-duration 1000 --list`.
- Expected: the parent process GeckoMain selected, as it was for the siteData3 profile of the same job.
- Got: `t-16 (GeckoMain, WebExtensions)` selected; the first query returned 54 IPCOut markers from that thread, which looked like an answer.
- Workaround: `thread select t-0` after every load.

## Question: "which of these interval markers are top-level (not nested inside another of the same name)"

- Command: `profiler-cli thread markers --search name:DocAccessible::PruneOrInsertSubtree --min-duration 50 --list --limit 0` gives 58 rows of ~370ms, most of them nested copies of the same recursive call.
- What the output could have shown: a nesting depth column, or a `--top-level` filter for recursive markers of the same name.
- Workaround: `--json` and a Python script that drops markers contained in the previous one.
