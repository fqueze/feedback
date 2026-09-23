## `--bugs` gives no answer when there is no bug

- Command: `fx-tests test browser/extensions/formautofill/test/unit/test_rust_autofill_address_parity.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test" line.
- Got: output byte-identical to the same command without `--bugs` (checked with `diff`), so it is impossible to tell "searched, found none" from "flag ignored".
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?quicksearch=<test file name>"` returned `[]`.
