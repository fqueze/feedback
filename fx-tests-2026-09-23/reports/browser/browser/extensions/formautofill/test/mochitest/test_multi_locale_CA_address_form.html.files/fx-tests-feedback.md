## `task --messages` shows TEST-KNOWN-FAIL info lines as the failure messages of a TIMEOUT

- Command: `fx-tests task H7LZmKs2T1KWqmTL-G-H8w --profiles --messages`
- Expected: the failing message, `Test timed out.` / `TIMEOUT Test exceeded time limit`.
- Got: only "The author of the test has indicated that flaky timeouts are expected. Reason: ..." (x2), which are `requestFlakyTimeout` TEST-KNOWN-FAIL lines logged on every run, pass or fail. Without `--messages`, that line is the only message shown under the TIMEOUT, which reads as the failure.
- Workaround: read the TEST-UNEXPECTED-FAIL marker in the profile.

## `test --bugs` finds no bug, although an open tracking bug names the test

- Command: `fx-tests test browser/extensions/formautofill/test/mochitest/test_multi_locale_CA_address_form.html --bugs`
- Expected: bug 1854981, "Intermittent browser/extensions/formautofill/test/mochitest/test_multi_locale_CA_address_form.html | single tracking bug" (REOPENED), which `fx-tests intermittent --bug 1854981 --since 21 --tree all` shows with 2 annotations in the window.
- Got: the normal `test` output, with no bug section and no "no bugs found" line either, so the absence reads as "no bug".
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=<test file name>"`.
