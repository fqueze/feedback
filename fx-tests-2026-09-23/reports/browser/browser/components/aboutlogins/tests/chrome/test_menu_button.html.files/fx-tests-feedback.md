## Question: how often does this test fail on test-macosx1500-aarch64-vms/opt-mochitest-chrome-1proc?

- Command: `fx-tests test browser/components/aboutlogins/tests/chrome/test_menu_button.html` (also `--coverage`, `--history`)
- Expected: failures on that config between 2026-09-16 and 2026-09-21, or at least a line saying some of its jobs could not be read.
- Got: `test-macosx1500-aarch64-vms/opt-mochitest-chrome-1proc   314   314   0   0   ok`, and `--history` showing failures only on 09-17, all from one `-cf` job. In reality the job was testfailed 46 of 85 times on Treeherder in that window, and sheriffs annotated 24 of those jobs on bug 2073189, each with test_menu_button failures. Those jobs were killed at max-run-time. `fx-tests task d6XM1k2_Sjquqb1U6OjEpQ` says so, but `fx-tests test` drops them without a word, even though their per-test profiles (`profile_test_menu_button.html.json`) were uploaded and name the test.
- What the output could have shown: a per-config count of jobs that were unreadable (killed at max-run-time), or failures counted from the per-test profile names in those jobs' artifact lists.
- Workaround: `treeherder-cli --repo autoland --similar-history <job id>`, plus `fx-tests intermittent --bug 2073189`.

## Question: which bug are this test's failures starred on?

- Commands: `fx-tests test <path> --bugs` and `fx-tests intermittent --test browser/components/aboutlogins/tests/chrome/test_menu_button.html`
- Expected: bug 2073189.
- Got: nothing ("no sheriff-annotated bug names the test"). The bug's summary names `test_confirm_delete_dialog.html`, but `fx-tests intermittent --bug 2073189` shows `24x browser/components/aboutlogins/tests/chrome/test_menu_button.html` under "Tests named, per annotated job".
- What the output could have shown: `--test` matching bugs whose annotated failure lines name the test, not only the bug summary.
- Workaround: the Treeherder `bug-job-map/?job_id=<id>` API for a failing job.
