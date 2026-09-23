## `--bugs` prints nothing when no bug is found

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_contentscript_csp.js --bugs`
- Expected: a `Bugs` section, saying "none" when there is none (and ideally listing closed bugs naming the test, e.g. bug 1618729 and bug 1780974, marked closed).
- Got: output identical to the run without `--bugs`; no section and no line saying nothing was found, so it is unclear whether the flag was honoured.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=<test file name>"`.
