# fx-tests feedback (browser_rules_edit-property-order.js)

## `fx-tests test <path> --bugs` prints nothing about bugs when none is open

- Command: `COLUMNS=250 fx-tests test devtools/client/inspector/rules/test/browser_rules_edit-property-order.js --bugs`
- Expected: a `Bugs` section, saying "none open" if so, ideally with the closed ones naming the test (here bug 1787030, the old single tracking bug, and bug 1772236, its duplicate).
- Got: output identical to the run without `--bugs`; no line says whether the lookup ran or found nothing.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=browser_rules_edit-property-order&include_fields=id,summary,status,resolution"`.
- Question the default output did not answer: "is there a bug for this test, open or closed".
