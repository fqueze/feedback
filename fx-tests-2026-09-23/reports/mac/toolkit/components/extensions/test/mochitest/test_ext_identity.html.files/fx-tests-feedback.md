## `--bugs` says nothing when it finds nothing

- Command: `fx-tests test toolkit/components/extensions/test/mochitest/test_ext_identity.html --bugs`
- Expected: a "Bugs" section, or a line saying no sheriff-annotated bug names the test.
- Got: the ordinary `fx-tests test` output with no bug section at all, so I could not tell "no bugs" from "the flag did nothing". `--json` showed `"annotatedBugs": []`.
- Workaround: `--json`, then a Bugzilla REST search on the summary (which found bug 1777016, a closed single-tracking bug still cited in the manifest's `skip-if`).
