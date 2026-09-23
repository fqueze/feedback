## `fx-tests test <path> --bugs` prints nothing when no bug names the test

- Command: `fx-tests test browser/components/urlbar/tests/browser/browser_speculative_connect_not_with_client_cert.js --bugs`
- Expected: a `Bugs: none` line (or the bugs).
- Got: the same output as without `--bugs`, with no bugs section at all, which reads the same as the query having silently failed.
- Workaround: none; assumed "none".
