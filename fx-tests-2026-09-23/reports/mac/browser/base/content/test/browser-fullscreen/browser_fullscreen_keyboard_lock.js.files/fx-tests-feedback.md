## Question: "has this test been failing before its directory was renamed, and which bug tracks it?"

- Command: `fx-tests test browser/base/content/test/browser-fullscreen/browser_fullscreen_keyboard_lock.js --history` and `... --bugs`
- Expected: history and bugs to follow the rename of `browser/base/content/test/fullscreen/` to `browser-fullscreen/` (bug 2069131, landed 2026-09-11), or at least a note that the path is new and had a predecessor.
- Got: `--history` shows zero runs before 2026-09-11, which reads as "new test"; `--bugs` printed nothing, though bug 2032941 ("Intermittent browser/base/content/test/fullscreen/browser_fullscreen_keyboard_lock.js | single tracking bug") exists.
- Workaround: found the rename with `git log`, then re-ran `fx-tests test` on the old path (18 more failures, 15 of them the same message), and found the bug with a Bugzilla REST quicksearch on the file name.
- What would have answered it: a "renamed from <old path> on <date>" line (the manifests' git history has it), with the old path's counts and bugs merged or listed.
