## `errors --message` cannot find a multi-line console.error

- Question: which tests/jobs print "remote browser crashed while on about:blank" (the parent's console.error when a tab's content process dies).
- Command: `fx-tests errors --message "remote browser crashed" --group-by test` (also `"crashed while on"`, `"remote browser"`), day 2026-09-20.
- Expected: the tests whose logs contain that console.error.
- Got: "No markers matched." In the job's resource-usage profile, the `console.error` marker has an empty Message; the text is in the following `output` markers ("  remote browser crashed while on", "  about:blank"). So the errors data presumably has the console.error with no text.
- Workaround: searched the job's live_backing.log by hand. Suggest joining a console.error's continuation lines into its message.

## `test --bugs` says nothing when no bug is found

- Command: `fx-tests test devtools/client/webconsole/test/browser/browser_webconsole_stubs_page_error.js --bugs`
- Expected: a "Bugs: none found" line.
- Got: the normal `test` output with no bugs section, which reads the same as the flag being ignored.
- Workaround: a Bugzilla quicksearch by hand.
