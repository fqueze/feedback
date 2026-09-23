## `--bugs` prints nothing when no bug is found
- Command: `fx-tests test services/sync/tests/unit/test_bookmark_engine.js --bugs`
- Expected: a "Bugs" block, or a line saying no sheriff-annotated bug names this test.
- Got: the ordinary report with no bugs section at all, so "none found" and "flag ignored / query failed" look the same.
- Workaround: Bugzilla REST `bug?summary=test_bookmark_engine` (found bug 1573709, FIXED 2022, and bug 1826913, RESOLVED INCOMPLETE).
