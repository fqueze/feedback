## `--bugs` prints no bug section, even when a bug names the test

- Command: `fx-tests test devtools/client/inspector/test/highlighter/browser_inspector_highlighter-02.js --bugs`
- Expected: a Bugs section listing bug 2001370 ("Intermittent devtools/client/inspector/test/highlighter/browser_inspector_highlighter-02.js | single tracking bug", RESOLVED INCOMPLETE), or an explicit "no bug names this test".
- Got: the same output as without `--bugs`, with no bug section and no line saying none was found.
- Workaround: `curl -sL "https://bugzilla.mozilla.org/rest/bug?summary=browser_inspector_highlighter-02&include_fields=id,summary,status,resolution"`.
