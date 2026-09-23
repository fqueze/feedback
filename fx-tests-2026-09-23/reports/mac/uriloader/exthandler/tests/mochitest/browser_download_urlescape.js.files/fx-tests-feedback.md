## `--bugs` prints nothing when no open bug names the test

- Question: "is there a bug for this test, open or closed?"
- Command: `fx-tests test uriloader/exthandler/tests/mochitest/browser_download_urlescape.js --bugs`
- Expected: a Bugs section, even if it only says "none open", ideally with the resolved tracking bugs too.
- Got: the same output as without `--bugs`, and no line about bugs. I could not tell "no bug" from "flag ignored".
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=browser_download_urlescape'`, which found 7 resolved bugs, the latest being bug 2006617 (single tracking bug, RESOLVED INCOMPLETE 2026-06-29). An explicit "no open bug; N resolved: ..." line would have answered it.
