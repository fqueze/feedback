## `--bugs` prints no bug section at all

- Command: `fx-tests test browser/components/customizableui/test/browser_940307_panel_click_closure_handling.js --bugs`
- Expected: a "Bugs" section, listing bug 1237034 ("browser_940307_panel_click_closure_handling.js fails 50% of the time in multi-core docker vs 0% in single core", NEW), or explicitly saying none was found and what was searched.
- Got: exactly the default output, with no bug section and no "none found" line, so I could not tell "no bug" apart from "flag ignored".
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?quicksearch=<test file name>`.

## Question: does any job of this config have a per-test profile for this test?

- Command: `fx-tests task <taskId> --profiles`, repeated on 5 TSan tasks.
- Got: "No failing test named a per-test profile in this job." each time.
- Could have shown: that the config never uploads per-test profiles (TSan), from `fx-tests test <path> --profiles` or a note in `task --profiles`, which would have saved checking the tasks one by one.
