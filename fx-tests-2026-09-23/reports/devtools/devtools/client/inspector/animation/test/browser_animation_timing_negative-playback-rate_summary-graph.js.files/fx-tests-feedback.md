## `fx-tests test <path> --bugs` prints no bug section at all

- Command: `fx-tests test devtools/client/inspector/animation/test/browser_animation_timing_negative-playback-rate_summary-graph.js --bugs`
- Expected: the bugs naming the test — here bug 1781308 ("Intermittent …summary-graph.js | single tracking bug", RESOLVED INCOMPLETE 2026-07-13) and its dup 1584482 — or an explicit "no bugs found (searched: open only / …)" line.
- Got: the same output as without `--bugs`; no Bugs section and no line saying none was found, so "no bug" and "flag ignored" look identical.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=negative-playback-rate&include_fields=id,summary,status,resolution"`.
