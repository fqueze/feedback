## One failure split into eight Issues rows, each printing its full stack

- Command: `fx-tests test browser/components/urlbar/tests/browser-newtab/browser_searchTelemetry.js` (and again with `--history`)
- Expected: one Issues row for "Uncaught exception in test - Error: Something tried to use the search service before it finished initializing", with its count (134), since every row is the same exception thrown from the same test line.
- Got: rows 2-9, split by line numbers that moved between revisions (`SearchService.sys.mjs:1530` vs `:1533`, `browser-test.js:1526` vs `:1543`) and by the async tail of the stack; each row printed its whole 25-line stack. 36 KB of output, most of it stack frames, and the same again with `--history`.
- Workaround: `COLUMNS=250 ... | grep -v` on the stack-frame lines.
- Question the default output did not answer: "how many distinct failure modes does this test have?" Grouping on the message with line numbers stripped, and showing the stack once, would answer it.

## The worst config has no per-test profiles, and nothing says so

- Command: `fx-tests task <taskId> --profiles` on four TSan tasks (EMgQZssATmG4hkJKlN5IXw, NnuuDwQtRAG0nIEuRRjX9w, d5274oA-RZmwku0lrV0TQg, LbRPAbe2T7yz9h2uLkKeSg)
- Expected: a per-test profile for the failure, or a note that this config never uploads one.
- Got: "No failing test named a per-test profile in this job." on each; the TSan config is 103 of the 135 failures.
- Workaround: tried other configs until one had profiles (Linux debug, Windows ASan).
- Question: "which failing jobs of this test have a per-test profile?" `fx-tests test <path> --task-ids` could mark the tasks that have one.
