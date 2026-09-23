## `fx-tests test <path> --bugs` prints nothing when no bug is found

- Command: `fx-tests test browser/components/urlbar/tests/browser-searchMode/browser_searchModeSwitcher_basic.js --bugs`
- Expected: a line such as "Annotated bugs: none" so that "no bug" is distinguishable from "the flag did nothing".
- Got: output identical to the run without `--bugs`; only `--json` shows `"annotatedBugs": []`.
- Workaround: `--json` and read `annotatedBugs`.

