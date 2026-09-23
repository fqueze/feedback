## Question: the full history of a test that was moved to another directory

- Command: `fx-tests test browser/base/content/test/browser-keyboard/browser_toolbarKeyNav.js --history`
- Expected: the history to include the runs under its previous path (`browser/base/content/test/keyboard/`, moved on 2026-09-11 by bug 2069131), or a note that the path has no data before a given day.
- Got: ten days of `0 pass 0 fail 0 skip` before 2026-09-11, which reads like "not running" rather than "renamed"; had to guess the old path and run the command again (and again with `--task-ids`).
- What the output could show: "no data under this path before 2026-09-11; <old path> has data until 2026-09-11" when a same-named test exists elsewhere.
