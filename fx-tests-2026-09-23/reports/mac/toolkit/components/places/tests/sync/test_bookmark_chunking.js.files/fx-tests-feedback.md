# fx-tests feedback (test_bookmark_chunking.js)

- Command: `fx-tests test toolkit/components/places/tests/sync/test_bookmark_chunking.js --bugs`.
  Expected: a "Bugs" block, or a line saying no sheriff-annotated bug names the test. Got: the same output as without `--bugs`, with nothing about bugs, so "none found" cannot be told from "flag ignored". Workaround: Bugzilla REST search on the file name (found bug 1865692 and bug 1955754, both RESOLVED INCOMPLETE).
- Question: "is the slowdown new, or has the test always run near its timeout on this config?". `fx-tests test <path> --durations` has no time axis, and `--history` has no durations. Workaround: loop `--durations --config aarch64-vms/debug --day <d>` over the 21 days. What the output could have shown: `--history --durations` giving the per-day median/p95/max next to the pass/fail counts.
