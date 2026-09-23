## `fx-tests task` counts expected TEST-FAIL results as failing markers

- Command: `fx-tests task cZNJf2x0Swi3pHC3bNrq_Q --profiles` (also `B9o66NPURJ2At18lrESXwA`, `PX8j74JzSuKyGciAO8iwJw`)
- Expected: 1 failing test, `AutocompleteTest#addressSelectDismiss`, the only `TEST-UNEXPECTED-FAIL` in the log.
- Got: "129 failing markers in this job named no test path" in all three jobs, then "0 tests, 0 executions, 0 failing". The other 128 are `test` markers with Status FAIL but no Expected field and color green (for example `AutofillDelegateTest#autofillIframe[#oop]`, logged as `TEST-FAIL ... took 99ms`), which are expected failures. The identical count in unrelated jobs is the tell.
- Workaround: `grep TEST-UNEXPECTED live_backing.log`; in the profile, the unexpected one is the marker with `Expected: PASS`.
- The geckoview-junit coverage gap itself (no run counts, `task` sees no tests) is already logged by the sibling reports for bugs 1808075 and 2058553; I hit it too and used `fx-tests intermittent --bug 1808064 --since 90 --limit 0` plus the 45 job logs.
