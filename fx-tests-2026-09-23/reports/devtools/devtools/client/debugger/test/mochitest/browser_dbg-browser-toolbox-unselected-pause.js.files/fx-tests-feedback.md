## `test --bugs` prints no bug section, silently

- Command: `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-browser-toolbox-unselected-pause.js --bugs`
- Expected: a list of bugs naming the test. Bugzilla has two: 1827742 ("Intermittent [TV] devtools/client/debugger/test/mochitest/browser_dbg-browser-toolbox-unselected-pause.js | single tracking bug", NEW) and 1937315 ("browser_dbg-browser-toolbox-unselected-pause.js perma failing on macosx 14.70", NEW).
- Got: the same output as without `--bugs` (exit 0, nothing on stderr), no "Bugs" heading, not even "no bugs found". Indistinguishable from the flag being ignored.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?quicksearch=browser-toolbox-unselected-pause'`.
