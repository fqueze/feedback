## Question: which bugs name this test?
- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_url_overrides_newtab.js --bugs`
- Expected: a list of bugs naming the test, including bug 1775584 ("Intermittent .../browser_ext_url_overrides_newtab.js | single tracking bug", RESOLVED INCOMPLETE, still starred on ESR in the last 30 days).
- Got: the same output as without `--bugs`, with no Bugs section and no line saying that none were found.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=browser_ext_url_overrides_newtab'`, then `fx-tests intermittent --bug 1775584 --since 30 --tree all`. The default `intermittent --bug` window (7 days, trunk) said "no sheriff annotations", which is misleading for a bug starred only on ESR.

