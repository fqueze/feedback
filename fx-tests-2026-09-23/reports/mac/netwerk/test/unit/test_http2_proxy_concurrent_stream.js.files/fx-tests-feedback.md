## `test --bugs` prints nothing when no bug is found

- Command: `fx-tests test netwerk/test/unit/test_http2_proxy_concurrent_stream.js --bugs`
- Expected: a line saying no sheriff-annotated bug names this test (it fails 6% of 9,968 runs, so "none" is itself a finding).
- Got: the same output as without `--bugs`, with no bugs section at all. Only `--json` showed `annotatedBugs: []`, so I could not tell "no bug" from "the lookup silently failed" without a script.
- Workaround: `--json | python3 -c ...annotatedBugs`, then a Bugzilla REST search on the summary.
