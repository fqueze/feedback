## `fx-tests test <path> --bugs` prints no bug section when there is none

- Command: `fx-tests test toolkit/components/glean/tests/browser/browser_fog_gpu.js --bugs`
- Expected: a "Bugs" section, even if it says none were found (Bugzilla has closed bugs 1743843 etc. naming the test).
- Got: the same output as without `--bugs`; no line saying whether bugs were searched or none matched.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?short_desc=browser_fog_gpu&short_desc_type=allwordssubstr`.
