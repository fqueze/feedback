## "Is there a bug naming this test?"

- Command: `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-console-map-bindings.js --bugs`
- Expected: a Bugs section, or an explicit "no bug names this test" line.
- Got: output identical to the run without `--bugs`; nothing says whether bugs were searched and none found.
- Workaround: Bugzilla REST quicksearch for the file name (returned no bugs).
