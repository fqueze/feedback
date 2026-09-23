# fx-tests feedback — browser_resources_network_events_parent_process.js

## A `todo()` (TEST-KNOWN-FAIL) is reported as the failure

- Command: `fx-tests test devtools/shared/commands/resource/tests/browser_resources_network_events_parent_process.js`,
  and `fx-tests task MUc9-ldYRuSrmi-cOAke4A --profiles` / `fx-tests task erIj2sHgR0Kp-4Wir081pQ --profiles`
- Expected: "Issues (first failure per run)" and the task headline to show the first
  TEST-UNEXPECTED-FAIL, `Failed waitFor(): Failed condition: () => receivedNetworkEvents.length == 5/6`.
- Got: `2x FAIL After bug 1076583, image load is async and we can't get a stack trace` as the
  only issue, and as the headline message in both tasks. In the profiles this message is a
  `TEST-KNOWN-FAIL` marker from a `todo()` call, which runs in every run, passing ones included.
  The real failure only shows with `fx-tests task <id> --messages`.
- Cost: the issue list pointed at a message that is not a failure, and gave no way to tell the
  two failing runs apart (length == 5 on linux, == 6 on mac).
- Workaround: `fx-tests task <id> --messages --full-messages`.

## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test <path> --bugs`
- Expected: a "Bugs" section, or a line saying no bug names the test.
- Got: output identical to the command without `--bugs`. You can't tell "no bug" from "flag ignored".
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=<test name>` (finds only
  closed bugs here: 1751214, 1751480, 1751893, 1767770).
