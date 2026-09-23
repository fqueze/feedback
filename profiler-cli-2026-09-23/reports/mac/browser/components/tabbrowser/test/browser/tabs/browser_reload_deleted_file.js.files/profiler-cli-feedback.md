## Question: how did one pref's value change over the browser session

- Command: `profiler-cli thread markers --search 'browser.uiCustomization.state' --list --limit 0 --session <s>`
- Expected: to see when the `nav-bar` placements inside `browser.uiCustomization.state` changed (a fresh vertical-tabs profile never gets `stop-reload-button`; after a `CustomizableUI.reset()` it does).
- Got: 67 to 117 rows, each with the whole multi-hundred-character JSON value, almost all identical to the previous row; the change points are unreadable by eye.
- Workaround: `--json` and a Python script that prints only the rows whose value differs from the previous one.
- What would have answered it: a changes-only view for `Preference Read`/`Preference Write` markers (e.g. `--distinct-values`, printing a row only when the pref's value differs from the last one seen).
## Question (again, in review): how did one pref's value change over the browser session

- Command: `profiler-cli thread markers --search browser.uiCustomization.state --list --limit 0 --session review-browser_reload_deleted_file.js-{2,3,5}`
- Expected: the points where the `nav-bar` placements inside the value changed, to check "never contains `stop-reload-button`" over 67 to 117 rows.
- Got: every row with the whole JSON value, truncated at the terminal width, almost all identical.
- Workaround: `--json` and a Python script printing only rows whose value differs from the previous one, three times, once per profile.
- What would have answered it: a changes-only view for `Preference Read`/`Preference Write` markers, as in the entry above.
