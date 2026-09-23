## fx-tests reports a TEST-KNOWN-FAIL as the failure message

- Command: `fx-tests test browser/extensions/formautofill/test/browser/creditCard/browser_creditCard_osAuth.js` (Issues, "first failure per run") and `fx-tests task cK2AYsCKQ_-8pK623KiVew --profiles` (the one-line message under the test).
- Expected: the run's TEST-UNEXPECTED-FAIL, `uncaught rejection: can't access property "chromeEventHandler", this._window.docShell is null`.
- Got: `handleEvent() was unable to perform a11y checks on hidden node: id: , tagName: button, className: button`. The per-test profile has that message as a `TEST-KNOWN-FAIL` (expected FAIL, an a11y-checks `todo`), logged 1.3 s before the real failure. All 6 failures are filed under it, so the failure mode's name is wrong, and grouping by message would merge this with any test that clicks a hidden button.
- Workaround: `fx-tests task <id> --messages` lists all four messages without saying which is unexpected; the per-test profile's `TEST-UNEXPECTED-FAIL` marker settles it.
