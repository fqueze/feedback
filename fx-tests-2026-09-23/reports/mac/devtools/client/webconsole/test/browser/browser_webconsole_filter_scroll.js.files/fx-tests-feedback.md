## `--bugs` prints nothing when no open bug matches, not even "none"

Question: is there a bug for this test's failure?

Command: `fx-tests test devtools/client/webconsole/test/browser/browser_webconsole_filter_scroll.js --bugs`

Expected: a Bugs section, even if it only says "no open bug names this test" (and ideally the closed ones: bug 1565831 and bug 1701097 both name the test in their summary).

Got: the same output as without `--bugs`, with no Bugs section and nothing on stderr, so I could not tell "no bug" from "the flag was ignored".

Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=browser_webconsole_filter_scroll&include_fields=id,summary,status,resolution'`.

