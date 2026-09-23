## A renamed test's history is split across two paths, with no pointer from one to the other

- Command: `fx-tests test browser/base/content/test/browser-favicons/browser_favicon_svg.js --history`
- Expected: the test's history, or a note that the file was renamed (bug 2069131 moved `browser/base/content/test/favicons/` to `browser-favicons/` on 2026-09-18) and that older runs are under the old path.
- Got: 3 days of data (2026-09-18 to 2026-09-20) and zeros before, which reads as "a new test". The 390 skips, the date the `skip-if` was removed (2026-09-10) and 201 of the 232 failures are only under the old path.
- Workaround: found the rename with `git log`, then re-ran every command with the old path and added the two sets of counts by hand.
