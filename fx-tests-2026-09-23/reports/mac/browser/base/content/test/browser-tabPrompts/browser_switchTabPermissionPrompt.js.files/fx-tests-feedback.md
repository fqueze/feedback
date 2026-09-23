## A renamed test's history stops at the rename, with no hint of the old path

- Command: `fx-tests test browser/base/content/test/browser-tabPrompts/browser_switchTabPermissionPrompt.js --history`
- Expected: the test's history across its rename (bug 2069131 moved `browser/base/content/test/tabPrompts/` to `browser-tabPrompts/` on 2026-09-16), or at least a line saying "no data before 2026-09-16; this file was at `<old path>`".
- Got: 1 timeout in 788 runs, zero runs before 2026-09-16, and nothing suggesting an earlier path. The old path held 3 more timeouts of the same mode and a 22-failure burst of another mode.
- Workaround: `git log` on the file to find the rename, then `fx-tests test <old path>` separately and add the two by hand.

## `--bugs` prints nothing when a bug names the test's old path

- Command: `fx-tests test browser/base/content/test/tabPrompts/browser_switchTabPermissionPrompt.js --bugs` (and the same for the new path)
- Expected: bug 2063730 ("Intermittent browser/base/content/test/tabPrompts/browser_switchTabPermissionPrompt.js | single tracking bug", 174 annotations in 30 days).
- Got: the usual summary with no bug section at all, not even "no bug found", so the flag looks ignored.
- Workaround: Bugzilla REST `bug?summary=switchTabPermissionPrompt&summary_type=substring`, then `fx-tests intermittent --bug 2063730 --since 30 --tree all`.
