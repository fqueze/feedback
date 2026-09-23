## `fx-tests test <path> --bugs` prints nothing when no bug is found (browser_ext_openPanel.js)

- Command: `COLUMNS=250 fx-tests test browser/components/extensions/test/browser/browser_ext_openPanel.js --bugs`
- Expected: a `Bugs` section, saying "none" when no bug names the test.
- Got: the same header, per-config table and Issues as without the flag, and no bug line at all, so "no bug" looks the same as "flag ignored".
- Workaround: searched Bugzilla REST `bug?summary=browser_ext_openPanel` (only bugs resolved in 2018-2020 came back).
