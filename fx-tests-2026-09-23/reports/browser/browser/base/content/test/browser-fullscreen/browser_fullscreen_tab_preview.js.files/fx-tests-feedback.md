## Question: how often does this test fail, across a file move?

- Command: `fx-tests test browser/base/content/test/browser-fullscreen/browser_fullscreen_tab_preview.js` (and `--history`, `--task-ids`)
- Expected: the failures from before bug 2069131 moved it from `browser/base/content/test/fullscreen/` included, or at least pointed at.
- Got: only the post-move 11 days (20 failures); the 35 earlier failures only appear by re-running every command with the old path, and the history of each path shows zeros for the other half of the window.
- Workaround: ran each command for both paths and added the numbers by hand.
- What would help: detect a test whose history starts mid-window and whose file name exists under another directory in the same window, and offer `--also <old path>` or merge them.
