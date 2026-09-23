## `fx-tests test` reports a TEST-KNOWN-FAIL (todo) as the failure message

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_persistent_storage_permission_indication.js`
- Expected: the Issues block to show the first TEST-UNEXPECTED-FAIL of each failing run, here `waiting for vsync to be disabled - timed out after 50 tries.`
- Got: `39x FAIL handleEvent() was unable to perform a11y checks on hidden node: id: identity-permission-box, ...`, which in the profile is a `TEST-KNOWN-FAIL` (AccessibilityUtils `a11yWarn` = `SimpleTest.todo`), logged in passing runs too. `fx-tests task --messages` lists it as "1x" among the failure messages as well. `fx-tests failures --message identity-permission-box` then says it is specific to this one test, which reinforces the wrong lead.
- Workaround: read the test's markers in the per-test profile (`--category Test --search <test>`) to see the status of each message.
- Cost: would have sent the diagnosis toward the permission box's visibility instead of the vsync cascade from another test.
- Correction to the entry above: "logged in passing runs too" was not verified (passing runs have no per-test profile). What is verified is that it is a `TEST-KNOWN-FAIL` in both failing profiles read (Xh6MnwM2R3OFkWAeuPuZLA, IT0S_eCQRJ-SkMnxZ3h6jA), logged before the real `TEST-UNEXPECTED-FAIL`s.
