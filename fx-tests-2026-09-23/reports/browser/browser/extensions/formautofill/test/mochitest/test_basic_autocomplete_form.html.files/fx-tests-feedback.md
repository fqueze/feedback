## Issues list shows "Failure details not recorded" for failures whose message is known

- Command: `fx-tests test browser/extensions/formautofill/test/mochitest/test_basic_autocomplete_form.html`
- Expected: the 8 main failures grouped under their message, `Test timed out.` / `[SimpleTest.finish()] No checks actually run. ...`.
- Got: `8x FAIL Failure details not recorded (likely Android or platform logging issue)`, and `--json` `messages` has `"(no message recorded)"`, count 8. But `fx-tests task <id> --messages` on any of those 8 tasks prints `[SimpleTest.finish()] No checks actually run. ...`, and the per-test profile holds both messages. These are Linux Wayland jobs, not Android.
- Workaround: run `fx-tests task --profiles --messages` on each task ID to learn the failure mode.

## Issue 2 is labelled with a TEST-KNOWN-FAIL line, not the failure

- Command: same as above, and `fx-tests task TRKpK1NQQz6LKwktdpuxDA --messages`.
- Expected: the debug failure listed as `Checking selected index - timed out after 50 tries. - Should not throw any errors`.
- Got: `2x FAIL The author of the test has indicated that flaky timeouts are expected.  Reason: Guarantee asynchronous identifyAutofillFields is invoked`. That is the `requestFlakyTimeout` TEST-KNOWN-FAIL line every such test logs. The real failure only shows in `task --messages`, as one of three lines. The same KNOWN-FAIL line also appears under the other tests of those jobs as their "message".
- Workaround: `fx-tests task <id> --messages` and ignore the "flaky timeouts are expected" lines.
