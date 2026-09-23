## A todo() message is reported as the failure

- Command: `fx-tests test devtools/shared/commands/resource/tests/browser_resources_network_events_parent_process.js` (and `fx-tests task erIj2sHgR0Kp-4Wir081pQ.0 --profiles`)
- Expected: the failure mode is `Failed waitFor(): ... receivedNetworkEvents.length == 5 ... timed out after 500 tries.`, the first TEST-UNEXPECTED-FAIL.
- Got: `Issues (first failure per run): 2x FAIL After bug 1076583, image load is async and we can't get a stack trace`. That is the test's `todo()` (TEST-KNOWN-FAIL) message, and it is also shown as that test's headline in `fx-tests task --profiles`.
- Workaround: `fx-tests task <id> --messages`, or the profile.
