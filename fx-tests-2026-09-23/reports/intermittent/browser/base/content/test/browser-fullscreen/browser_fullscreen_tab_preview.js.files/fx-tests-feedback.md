## Test history does not follow a file rename

- Question: how often has this test failed over the window, given it moved from `browser/base/content/test/fullscreen/` to `browser/base/content/test/browser-fullscreen/` on 2026-09-11?
- Command: `fx-tests test browser/base/content/test/browser-fullscreen/browser_fullscreen_tab_preview.js --history`
- Expected: the history of the test, or a note that the path is new and which old path it continues.
- Got: zero runs before 2026-09-11 with no hint of a rename; I only found the older 35 timeouts because `fx-tests intermittent --bug 2030804 --since 30 --tree all` listed the old path.
- Workaround: ran `fx-tests test` again on the old path and added the two by hand.
