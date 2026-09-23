# fx-tests feedback (from diagnosing layout/reftests/bugs/501627-1.html, bug 2063536)

## No reftest data for `fx-tests test`

- Command: `fx-tests test layout/reftests/bugs/501627-1.html`
- Expected: rates per config, history, failing task IDs for a reftest.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...". `--harness` only takes xpcshell|mochitest.
- Workaround: Treeherder `failuresbybug`, `failurecount` and `project/autoland/jobs/?job_type_name=...` APIs, then grepping ~600 `live_backing.log` files for the test's line. Cost: most of the investigation's wall time.

## Question the tools could not answer: "the raw image-comparison values of this reftest in every run, passing runs included"

- The failure here is a reftest whose test image takes one of several deterministic variants per run. Only the failing variant shows in annotations; the passing variants (and the pre-autofuzz values, logged as `REFTEST wr-on-android dropping fuzz of (a, b) to (0, 0)` on Android devices) are only in the full logs.
- Command used: `curl live_backing.log | grep -m1 -B3 "TEST-(PASS|UNEXPECTED-FAIL)... | layout/reftests/bugs/501627"` over every job of the config.
- What would have answered it: a per-run table of (status, max difference, differing pixels, and the pre-autofuzz values) for a reftest, over time. That is what exposed the regression date and the 2026-09-20 value shift.

## `fx-tests task` warning misattributes expected-fail reftests against about:blank

- Command: `fx-tests task OJ3yCQM6Q6Wu9Hg4q93vyA --profiles`
- Got: "warning: 6 failing markers in this job named no test path and are not in the table below (a crash recorded against a manifest has no test to attribute it to): FAIL layout/reftests/bugs/446100-1a.html == about:blank, ..."
- Expected: these are `TEST-KNOWN-FAIL` reftests (`446100-1*.html == about:blank`), not failures and not crashes. The warning suggests a crash that did not happen.
