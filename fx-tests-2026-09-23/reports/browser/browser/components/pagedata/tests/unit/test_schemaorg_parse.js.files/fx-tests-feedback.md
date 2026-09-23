## Question: which tests hit this crash signature?

- Command: `fx-tests crashes --harness xpcshell --signature "storage::Service::Observe" --limit 0`
- Expected: the 28 tests behind the signature, with counts (to see whether the crash is specific to my test or tree-wide).
- Got: one row `327  28  0  @ mozilla::storage::Service::Observe` and no test names.
- Workaround: `--json`, where `rows[].tests[]` has them. The default output could list the tests (top N with counts) under each signature.

## Question: does this test have a bug?

- Command: `fx-tests test browser/components/pagedata/tests/unit/test_schemaorg_parse.js --bugs`
- Expected: a Bugs section, or an explicit "no bug names this test".
- Got: the normal output with no Bugs section at all, so no way to tell "none found" from "flag ignored".
- Workaround: Bugzilla quicksearch on the crash message found bug 2061660.

## Question: what did the crashed test print (crash annotation / "Storage connection not closed: X")?

- Command: `fx-tests crash WhKsWGhIQmysId7--mM6OA.0 25f6f822-f5ea-4d49-a452-e40e1c303d76` (also `--json`, `--raw`)
- Expected: crash annotations, here `StorageConnectionNotClosed`, which names the database.
- Got: stack only; no annotations in any output mode.
- Workaround: found the `Storage connection not closed: places.sqlite` output line in the resource-usage profile's markers.
