## `fx-tests test <path> --bugs` says nothing when no bug is found

- Command: `fx-tests test browser/components/firefoxview/tests/browser/browser_recentlyclosed_firefoxview.js --bugs`
- Expected: a "Bugs" section, even if it only says "none found".
- Got: the same output as without `--bugs`, so there was no way to tell "no bug" from "flag ignored".
- Workaround: searched Bugzilla REST by summary; it found only bug 1875879, closed INCOMPLETE.
