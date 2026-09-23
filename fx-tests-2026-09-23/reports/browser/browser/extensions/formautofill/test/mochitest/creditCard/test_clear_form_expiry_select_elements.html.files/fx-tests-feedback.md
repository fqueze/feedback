## `fx-tests test --bugs` prints nothing when the only bugs naming the test are closed or not "Intermittent" ones

- Command: `fx-tests test browser/extensions/formautofill/test/mochitest/creditCard/test_clear_form_expiry_select_elements.html --bugs`
- Expected: a Bugs section, or an explicit "no open bug names this test". Bugzilla has bug 1902536 (the "single tracking bug" for this test, RESOLVED INCOMPLETE 2026-08-03) and bug 1758142 (NEW, "Fix dummy task workaround in test_clear_form_expiry_select_element", about this very race).
- Got: the same output as without `--bugs`; no Bugs section and no line saying none was found, so "none" and "the flag did nothing" look the same.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=test_clear_form_expiry`.
