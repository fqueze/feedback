## Question: "the failure rate and per-config table of a test that was renamed inside the window"

- Commands: `fx-tests test browser/base/content/test/browser-pageinfo/browser_pageinfo_images.js` and `fx-tests test browser/base/content/test/pageinfo/browser_pageinfo_images.js` (moved by bug 2069131 on 2026-09-11).
- Expected: one view of the test's 21 days, or at least a hint that the history continues under the old path. Nothing in the new path's output mentions the old one, and its `--history` shows 0 runs before 2026-09-11, which reads like a new test.
- Got: two disjoint halves, each with its own per-config table and worst config (Windows standalone at 11.8% on one, asan-swr at 11.2% on the other).
- Workaround: ran both with `--limit 0`, then summed the per-config rows with awk (`rg '^\s+test-' | awk '{c[$1]+=$3; r[$1]+=$5} ...'`). Combined, the worst config is asan-swr at 36/351.
- What would have helped: follow manifest moves (the index has both paths), or accept several paths and merge them.
