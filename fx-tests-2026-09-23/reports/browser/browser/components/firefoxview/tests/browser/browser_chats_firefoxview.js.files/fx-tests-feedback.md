## `fx-tests test <path> --bugs` prints nothing about bugs

- Command: `fx-tests test browser/components/firefoxview/tests/browser/browser_chats_firefoxview.js --bugs`
- Expected: a Bugs section, with bugs naming the test or an explicit "no bug names this test".
- Got: the same output as without `--bugs`, with no Bugs section at all. Bugzilla has two bugs naming the test (2013575 WONTFIX, 2023803 INCOMPLETE). Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=browser_chats_firefoxview"`.
