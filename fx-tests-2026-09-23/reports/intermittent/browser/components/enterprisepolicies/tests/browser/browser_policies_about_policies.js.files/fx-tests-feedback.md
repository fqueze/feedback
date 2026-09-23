# fx-tests feedback (browser_policies_about_policies.js, bug 2069286)

## `fx-tests test` reports "passing" for a test whose only failures are test-verify jobs inside its window

- Command: `fx-tests test browser/components/enterprisepolicies/tests/browser/browser_policies_about_policies.js` (and `--history`)
- Expected: the 9 test-verify (TV) failures of 2026-09-04 (autoland 39e5fc8f6fd9, e.g. task MbliJuWUTruKHUrO5zVY0Q) counted, or at least a note that TV jobs are not in the data.
- Got: "6,613 runs 6,613 pass (100.00%) 0 fail ... Verdict: passing", history shows 0 fail on 2026-09-04, with no hint that test-verify is excluded.
- Workaround: `fx-tests intermittent --bug 2069286 --since 30 --tree all` found the 9 TV jobs.

## `fx-tests task --profiles` says "Passed when the harness reran it" when the rerun failed the same way

- Command: `fx-tests task MbliJuWUTruKHUrO5zVY0Q --profiles` (same for PHKrWUFGRX-rTYeOeuxi6A)
- Expected: "the rerun failed too" — a `-2` profile exists, and the resource-usage profile has a second `FAIL ... The failure is not listed twice - 2 == 1` in the rerun (t=20.584s), after which TV step 1 is reported `FAIL`.
- Got: "FAIL — 2 failing executions of 4 / Passed when the harness reran it." In test-verify each browser runs the test repeatedly; iteration 1 of the rerun passed, iteration 2 failed. The line seems to be driven by any PASS execution after the first failure.
- Workaround: read the `-2` profile and the resource-usage profile.
