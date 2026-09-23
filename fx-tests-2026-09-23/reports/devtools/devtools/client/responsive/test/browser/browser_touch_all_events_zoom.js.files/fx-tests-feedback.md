## `fx-tests test <path> --bugs` prints no bug section at all

- Command: `fx-tests test devtools/client/responsive/test/browser/browser_touch_all_events_zoom.js --bugs --full-messages`
- Expected: a "Bugs" section, or a line saying no bug names the test.
- Got: output byte-identical to the same command without `--bugs`.
- Workaround: took the bug numbers from the manifest's skip-if comments (1977557, 1977558).

## `fx-tests try <rev> --task-ids --profiles --limit 0` still truncates the per-test task and profile lists

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --task-ids --profiles --limit 0 --full-messages --messages`
- Expected: with `--limit 0`, every task ID and profile URL of the row.
- Got: `… 19 more tasks`, `… 7 more profiles`.
- Workaround: `--json` and a script over `permaFails[].taskIds`/`profiles`.
- Question the default output could not answer: "which task IDs, on which config, are behind this one test's failures on my push".

