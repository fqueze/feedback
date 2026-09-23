## `--bugs` with no match prints nothing about bugs

- Command: `fx-tests test dom/workers/test/xpcshell/test_ext_redirects_sw_scripts.js --bugs`
- Expected: a "Bugs" section, saying "none found" when no bug names the test.
- Got: output identical to the run without `--bugs`, so I could not tell "searched, found
  none" from "flag ignored".
- Workaround: Bugzilla REST `quicksearch=<file name>` by hand (found only an unrelated
  enhancement bug).
