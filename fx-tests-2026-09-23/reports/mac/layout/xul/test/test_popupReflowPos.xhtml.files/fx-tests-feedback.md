## `--bugs` prints nothing, not even "no bug found"

- Command: `fx-tests test layout/xul/test/test_popupReflowPos.xhtml --bugs`
- Expected: bug 1895350, "Intermittent layout/xul/test/test_popupReflowPos.xhtml | single tracking bug" (RESOLVED INCOMPLETE), or a line saying no open bug names the test.
- Got: the same output as without `--bugs`, with no bug section at all.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=popupReflowPos'`.
