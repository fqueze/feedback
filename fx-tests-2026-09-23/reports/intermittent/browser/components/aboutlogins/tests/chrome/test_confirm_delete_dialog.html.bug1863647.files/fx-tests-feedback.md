## Question: "Which failures of this test does fx-tests not count?"

- Command: `fx-tests test browser/components/aboutlogins/tests/chrome/test_confirm_delete_dialog.html` (and `--coverage`)
- Expected: the 10 macosx1500-aarch64-vms opt mochitest-chrome-1proc jobs sheriffs starred on bug 1863647 (2026-09-16..20), where this test timed out on both first run and retry, to appear as failures/timeouts, or at least a note that N jobs of this config could not be read.
- Got: `test-macosx1500-aarch64-vms/opt-mochitest-chrome-1proc 314 runs 314 pass 0 fail`, verdict built only on the single Linux assertion job. Only `fx-tests intermittent --bug` revealed the mac mode. `fx-tests task LED-4GBVS0KmSixMOZZA3Q` then refuses ("killed for exceeding its maximum duration ... partial stream"), even though the task has per-test profiles (`profile_test_confirm_delete_dialog.html.json`, `-2`) and `mochitest-chrome_errorsummary.log` naming them.
- Workaround: listed the task artifacts with the Taskcluster API and read `mochitest-chrome_errorsummary.log` by hand.
- Could have shown: in `fx-tests test`, a line "N jobs of this config hit max-run-time and were not read"; in `fx-tests task`, fall back to errorsummary.log + per-test profile artifacts when the resource profile is partial.
