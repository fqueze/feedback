## `--bugs` finds no bug for a test whose tracking bug names it

- Command: `fx-tests test devtools/client/webconsole/test/browser/browser_jsterm_autocomplete_getters_cache.js --bugs`
- Expected: bug 1784791 ("Intermittent devtools/client/webconsole/test/browser/browser_jsterm_autocomplete_getters_cache.js | single tracking bug", RESOLVED INCOMPLETE by BugBot, reopened twice since), and its older duplicate 1731944.
- Got: the ordinary `test` output, with no Bugs section and no line saying none was found, so "no bug" and "flag ignored" look the same.
- Workaround: Bugzilla REST `bug?short_desc=Target%20already%20destroyed&short_desc_type=allwordssubstr`, which found 1731944, then its `dupe_of`. `fx-tests intermittent --bug 1784791 --since 21 --tree all` then showed 2 annotations, both on ESR debug.
