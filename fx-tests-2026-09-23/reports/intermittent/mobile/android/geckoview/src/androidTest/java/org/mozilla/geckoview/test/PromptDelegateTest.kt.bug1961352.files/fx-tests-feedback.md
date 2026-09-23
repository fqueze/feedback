## Question: "how often does this GeckoView junit test fail, and on which configs?"

- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/PromptDelegateTest.kt`
- Expected: rates per config, as for mochitest/xpcshell (the parent brief routes GeckoView junit tests here).
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...` — junit is not covered, so there is no run count, only sheriff annotations from `fx-tests intermittent --bug`.
- Workaround: `fx-tests intermittent --bug 1961352 --since 21 --limit 0`, then Treeherder's `/api/jobs/?push_id=` to find passing geckoview-junit jobs to compare with.
- Also: `fx-tests task <taskId> --profiles` on a junit job says "This profile records no tests at all", while `profile_resource-usage.json` does hold 1457 `test` markers with PASS/FAIL status (e.g. `m-64 test FAIL — org.mozilla.geckoview.test.PromptDelegateTest#directoryTest`). The warning lists them as "133 failing markers named no test path". Reading those markers (the junit test ids are class#method, not paths) would give per-job outcomes for junit.
- The "133 failing markers" warning on `fx-tests task KWch9aFFTLaMqvPNUfwkmw --profiles` counts 132 `test` markers with status FAIL and no `expected` field (known/expected junit failures) plus the one real FAIL (expected PASS). Only the last is a failure; the warning makes the job look like it had 133.
