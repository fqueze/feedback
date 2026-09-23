## `--bugs` prints nothing when the only bugs naming the test are resolved

- Command: `fx-tests test devtools/client/inspector/fonts/test/browser_fontinspector_editor-variable.js --bugs`
- Expected: the two bugs whose summary names the test, bug 2045901 (RESOLVED INCOMPLETE, still annotated on beta in the window) and bug 2045917 (RESOLVED FIXED in June, the last fix to this test), or at least a line saying none were found / resolved ones are hidden.
- Got: the normal `test` output with no Bugs section at all, so "no bug" and "flag ignored" look identical.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=fontinspector_editor-variable"`.
