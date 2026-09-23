## `fx-tests test <path> --bugs` prints nothing when no bug is found

- Command: `fx-tests test devtools/client/inspector/rules/test/browser_rules_edit-property-order.js --bugs`
- Expected: a "Bugs" section, saying "none open" or listing the resolved ones (Bugzilla has 4 resolved bugs naming this test, e.g. 1787030).
- Got: the same output as without `--bugs`; no section at all, so I could not tell "no bug" from "flag ignored".
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=<test file name>`.

## `fx-tests task --messages` lists a TEST-KNOWN-FAIL todo as a failure message

- Command: `fx-tests task SazzLziUQPeKYcvZaOt8mw --profiles --messages`
- Expected: only the unexpected results (`Test timed out`).
- Got: `1x handleEvent() was unable to perform a11y checks on hidden node: ...` listed first, above the timeout. In the profile that line is a `TEST-KNOWN-FAIL` emitted on passing steps too, so it reads as the failure when it is not. It is also the headline message of the test in `fx-tests task` without `--messages`.
- Workaround: checked the marker status in the profile.

