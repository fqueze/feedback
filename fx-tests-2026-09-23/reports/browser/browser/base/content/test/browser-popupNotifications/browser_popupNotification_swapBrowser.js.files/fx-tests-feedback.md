## Question: the failures of a test that was renamed/moved within the window

- Command: `fx-tests test browser/base/content/test/browser-popupNotifications/browser_popupNotification_swapBrowser.js` (and `--history`, `--task-ids`)
- Expected: the test's failures over the whole window, since bug 2069131 only moved the file on 2026-09-11.
- Got: only the 18 timeouts since the move; the other 37 are only under the old path `browser/base/content/test/popupNotifications/...`, so every command had to be run twice and the rates and per-config counts summed by hand.
- Workaround: run each command on both paths and add up.
- What the output could show: a note "this file was at <old path> until <date>: N more failures there" (the rename is in the manifest/moz.build history), or an option to merge both paths.
