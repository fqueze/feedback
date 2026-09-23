## `fx-tests test` counts a TEST-KNOWN-FAIL (todo) as a run's first failure

- Command: `fx-tests test devtools/client/framework/test/browser_toolbox_rulers_button_highlighter_reload.js`
- Expected: the Issues block ("first failure per run") lists the TEST-UNEXPECTED-FAIL that made each run fail.
- Got: issue 1, 174x, is `handleEvent() was unable to perform a11y checks on hidden node: id: command-button-rulers ...`. In the per-test profile of task FN0yyKEeT86FLshnIo3tyg (one of those 174), that message is a `TEST-KNOWN-FAIL` marker (AccessibilityUtils `a11yWarn()` is `SimpleTest.todo(false, ...)`); the only `TEST-UNEXPECTED-FAIL` is the `uncaught rejection: Connection closed ... updateConfiguration` message, which the tool files under issue 2's family instead. So issue 1 is not a failure mode at all, and the real failure mode (issues 2-14, ~150x) is undercounted by about half and looks Linux-only when it is on every platform.
- `fx-tests task FN0yyKEeT86FLshnIo3tyg --messages` also lists both messages side by side, with no status to tell the todo from the failure.
- Workaround: load the per-test profile and read the `TEST-*` marker names.
- Cost: I initially planned a separate diagnosis of an "a11y hidden node" failure mode that does not exist.

## One failure split into 14 Issues rows by line-number drift

- Command: `fx-tests test devtools/client/framework/test/browser_toolbox_rulers_button_highlighter_reload.js`
- Got: issues 2-14 are the same `uncaught rejection: Connection closed ... updateConfiguration` message, split by `toolbox.js` line numbers in the request stack (1743/1744/1745/1747, 2536/2541/2543/2545 — the file changed across revisions) and by how deep the harness part of the stack goes. Counts 58, 19, 17, 16, 15, 9, 7, 5, 3, 3, 1, 1, 1, 1: I had to add them by hand to know the failure's size.
- What the output could show: group rows whose messages differ only in `:line:col` of stack frames, with the total.

