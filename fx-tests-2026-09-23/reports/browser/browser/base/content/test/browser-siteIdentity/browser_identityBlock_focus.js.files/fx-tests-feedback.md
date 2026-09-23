## `--bugs` prints no bugs section at all when none match

- Command: `fx-tests test browser/base/content/test/browser-siteIdentity/browser_identityBlock_focus.js --bugs` (same for the old `siteIdentity/` path)
- Expected: a "Bugs" section, or an explicit "no bug names this test" line.
- Got: the ordinary `fx-tests test` output, identical to running without `--bugs`; nothing says whether the search ran or found nothing.
- Workaround: queried Bugzilla REST by summary (`/rest/bug?summary=browser_identityBlock_focus`), which found tracking bug 2029244 (RESOLVED INCOMPLETE) — so either the flag skips closed bugs silently or it did not run.
