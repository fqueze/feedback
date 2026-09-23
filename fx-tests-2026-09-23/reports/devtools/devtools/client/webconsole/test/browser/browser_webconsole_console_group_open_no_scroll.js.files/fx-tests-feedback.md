## Question: is there a bug that names this test

- Command: `fx-tests test devtools/client/webconsole/test/browser/browser_webconsole_console_group_open_no_scroll.js --bugs`
- Expected: a "Bugs" section, or a line saying no bug names the test.
- Got: the same output as without `--bugs`, with no bug section at all, so "no bug" and "flag ignored" look the same.
- Workaround: assumed none (the list builder also recorded no bug for this row).
