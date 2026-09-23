## `--bugs` prints nothing when no open bug matches

- Command: `fx-tests test devtools/client/webconsole/test/browser/browser_webconsole_location_debugger_link.js --bugs`
- Expected: a "Bugs" section, even if only "none open", ideally listing recently closed ones naming the test (bug 1329080, "Intermittent ... browser_webconsole_location_debugger_link.js | Test timed out", RESOLVED INCOMPLETE on 2026-08-17, is exactly this failure mode).
- Got: the standard `test` output with no bugs section and no line saying the search ran and found nothing, so "no bug" and "flag ignored" look the same.
- Workaround: `curl -sL "https://bugzilla.mozilla.org/rest/bug?summary=<test file name>&include_fields=id,summary,status,resolution,last_change_time"`.
