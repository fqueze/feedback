## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test devtools/server/tests/xpcshell/test_addon_debugging_connect.js --bugs`
- Expected: a "Bugs" section, or an explicit "no sheriff-annotated bug names this test" line.
- Got: the same output as without `--bugs`, with no bug section and nothing on stderr, so I could not tell "no bug" apart from "the lookup silently failed".
- Workaround: `curl -s "https://bugzilla.mozilla.org/rest/bug?summary=test_addon_debugging_connect&include_fields=id,summary,status,resolution"`, which found bug 1834256 (the tracking bug, closed INCOMPLETE on 2026-08-31).
