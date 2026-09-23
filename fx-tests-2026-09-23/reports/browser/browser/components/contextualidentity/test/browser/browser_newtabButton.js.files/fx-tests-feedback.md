## `--bugs` prints nothing when no annotated bug is found

- Command: `fx-tests test browser/components/contextualidentity/test/browser/browser_newtabButton.js --bugs`
- Expected: a Bugs section, even if it says "no sheriff-annotated bug names this test in the window" (Bugzilla has 7 bugs naming it, all resolved; bug 1775706 is the one its `skip-if` cites).
- Got: the exact same output as without `--bugs`; no line mentions bugs at all, so "none found" and "flag ignored" look identical.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=browser_newtabButton`.
