## Question: is there a bug for this test?
- Command: `fx-tests test browser/components/resistfingerprinting/test/browser/browser_cross_origin_isolated_reduce_time_precision.js --bugs`
- Expected: a list of bugs naming the test, or an explicit "no bugs found".
- Got: the normal `test` output with no bugs section at all, so I could not tell "no bug" from "flag ignored".
- Workaround: Bugzilla REST `bug?summary=cross_origin_isolated_reduce_time_precision`, which found tracking bug 1781850 (RESOLVED INCOMPLETE 2026-08-03) and two older ones.
