## Question: which failure messages are behind a TIMEOUT issue row?

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_native_messaging_portal.js`
- Expected: the Issues block to split the 242 timeouts by the first failure message logged before the timeout (this test fails an assertion, then hangs until the harness kills it).
- Got: one row, `242x TIMEOUT Test exceeded time limit`. The actual modes (124x `Got the sender name`, all ccov; 68x `The CreateSession mock was called`, all non-ccov; 7x `Non availability of the portal was logged`; 8x no message) are only visible per job with `fx-tests task <id> --messages`.
- Workaround: a shell loop running `fx-tests task <id> --messages` over the 207 task IDs from `--task-ids --limit 0` and grepping the first message (xargs -P 6, a few minutes).
- What could have shown it: `fx-tests test <path>` Issues rows keyed on the first TEST-UNEXPECTED message of a timed-out run (or a `--messages` flag on `test` that does that breakdown, with configs).
