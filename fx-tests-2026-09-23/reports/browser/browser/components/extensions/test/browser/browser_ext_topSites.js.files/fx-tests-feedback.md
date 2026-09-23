## `fx-tests test <path> --bugs` prints nothing when no bug is annotated

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_topSites.js --bugs`
- Expected: a "Bugs" section, saying "none annotated in the window" when empty.
- Got: output identical to the run without `--bugs` (checked with `diff`); only `--json` showed `"annotatedBugs": []`. It reads as if the flag was ignored.
- Workaround: `--json`, then a Bugzilla summary search, which found closed bugs naming the test (bug 2030542, bug 1775583, bug 1697190).
