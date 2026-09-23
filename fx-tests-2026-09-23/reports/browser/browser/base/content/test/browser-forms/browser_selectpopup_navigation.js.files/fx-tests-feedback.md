## `--bugs` silently finds nothing when the only bug is resolved

- Command: `fx-tests test browser/base/content/test/forms/browser_selectpopup_navigation.js --bugs` (and the same for the new `browser-forms/` path)
- Question: which bug tracks this test's intermittent failures?
- Expected: the tracking bug, 2012541 ("Intermittent browser/base/content/test/forms/browser_selectpopup_navigation.js | single tracking bug", RESOLVED INCOMPLETE by BugBot on 2026-06-22), or at least a "Bugs: none found" line.
- Got: the normal `test` output with no Bugs section at all, so it is unclear whether the flag did anything.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=browser_selectpopup_navigation'`. A resolved tracking bug is still useful: it says whether the failure was reported before and whether anyone analysed it.
