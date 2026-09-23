## `test --bugs` finds no bug for a test that has a tracking bug

- Command: `fx-tests test browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_yelp.js --bugs`
- Expected: bug 2067961, "Intermittent browser/components/urlbar/tests/quicksuggest/unit/test_quicksuggest_yelp.js | single tracking bug" (filed 2026-09-01, NEW), and the older 1880406 ("Intermittent TV ... | single tracking bug").
- Got: the same output as without `--bugs`; no bug section, not even a "no bugs found" line.
- Workaround: `curl -sL "https://bugzilla.mozilla.org/rest/bug?summary=test_quicksuggest_yelp&include_fields=id,summary,status"`.

## `intermittent --bug` attributes a Linux opt timeout bug to unrelated messages

- Command: `fx-tests intermittent --bug 2067961 --since 21 --tree all`
- Expected: annotations for the yelp test's TIMEOUT, on linux2404-64/opt and artifact/debug where it fails most.
- Got: 28 annotations, "28x linux2404-64-shippable opt", failure messages "xpcshell return code: 0", "afterFlush ... encrypted cache ...", "test_check_form_autofill_module_detect". The default window (`--bug 2067961` alone) says no annotations on trunk in the last 7 days, though the test timed out 60+ times in that window.
- Workaround: none; reported the counts as the tool gave them.
