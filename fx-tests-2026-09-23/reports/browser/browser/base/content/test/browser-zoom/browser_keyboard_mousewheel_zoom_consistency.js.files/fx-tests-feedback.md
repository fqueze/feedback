## `test --bugs` prints nothing when no bug is annotated

- Command: `fx-tests test browser/base/content/test/browser-zoom/browser_keyboard_mousewheel_zoom_consistency.js --bugs` (same for the old `zoom/` path)
- Expected: a "Bugs" section, saying "none annotated" when empty.
- Got: the same output as without `--bugs`, no section at all, nothing on stderr; only `--json` showed `"annotatedBugs": []`. Looked like the flag was ignored.
- Workaround: `--json`, then a Bugzilla quicksearch on the test name, which found bug 2010509 (NEW, no annotations in the window).

