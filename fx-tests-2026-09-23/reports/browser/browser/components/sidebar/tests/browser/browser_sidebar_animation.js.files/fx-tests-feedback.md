## `--bugs` prints nothing about bugs

- Command: `fx-tests test browser/components/sidebar/tests/browser/browser_sidebar_animation.js --bugs`
- Expected: a Bugs section listing bug 2067061 ("Intermittent browser/components/sidebar/tests/browser/browser_sidebar_animation.js | single tracking bug", NEW, filed 2026-08-27), or an explicit "no bug names this test".
- Got: output byte-for-byte the same as without `--bugs` (29 lines, no mention of any bug on stdout or stderr).
- Workaround: `curl -sL "https://bugzilla.mozilla.org/rest/bug?summary=browser_sidebar_animation&include_fields=id,summary,status"`.
