## The 19 "TIMEOUT Test exceeded time limit" runs are three different failures, and nothing in the output tells them apart

- Question: "which of this test's timeouts are the same failure?"
- Command: `fx-tests test browser/extensions/formautofill/test/mochitest/creditCard/test_basic_creditcard_autocomplete_form.html --task-ids --limit 0 --issue 2`
- Expected: some discriminator per run, e.g. the last `INFO` line before the timeout, or a JS error logged by the test's browser in the test's time span.
- Got: one issue, `19x TIMEOUT Test exceeded time limit`, and a list of task ids.
- Workaround: downloaded all 19 `live_backing.log` and grepped each for `savedFieldNames is null`, `activeWindow is null` and `Unable to restore focus`. That split them 13 / 5 / 1, on different platforms and dates.
- Could show: for a timeout, the test's last log line, and the JavaScript errors logged between its TEST-START and its timeout, grouped across runs.
