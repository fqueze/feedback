## `fx-tests test` on a renamed test shows it passing, with no hint of the old path

- Command: `fx-tests test browser/base/content/test/browser-contextMenu/browser_copy_link_to_highlight_viewsource.js`
- Expected: the test's failures (23 timeouts in the window), or at least a note that runs before 2026-09-16 are filed under another path.
- Got: `482 runs 482 pass`, `Verdict: passing`, `--history` with zero runs before 2026-09-16. The file was renamed from `browser/base/content/test/contextMenu/` on 2026-09-16/18 (bug 2069131, landed, backed out, relanded); all 23 failures are under the old path, which only a `git log --follow` revealed.
- Workaround: `git log --follow --name-status` on the file, then `fx-tests test <old path>`.
- What would have answered it: when a path's first run is well inside the window, say so ("first run 2026-09-16; renamed from ...?"), or fold renames in the way the parent's list already does by hand.
