# fx-tests feedback (test_bug570341.html)

## Question: "how many of this test's failures are the same failure mode, numbers aside?"

- Command: `fx-tests test docshell/test/mochitest/test_bug570341.html --limit 0`
- Expected: one Issues row, `Expected responseStart to happen before domLoading, got responseStart = N, domLoading = N` — 89x.
- Got: 89 rows of `1x`, each `↑ same as 1, but = <timestamp>, domLoading = <timestamp>`. The default (limit 10) shows 10 rows of `1x`, which reads as ten different failures.
- Workaround: `--limit 0`, then replace 13-digit numbers with `N` and `sort | uniq -c` in the shell. Also computed `responseStart - domLoading` over all rows (always 1), which the grouped row could have shown as a range of the differing numbers.
