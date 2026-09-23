## `fx-tests test <path> --bugs` prints nothing about bugs when there are none

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_aiwindow_assistant_message_footer.js --bugs`
- Expected: a "Bugs" section, or a line saying "no bug names this test".
- Got: output identical to the run without `--bugs`, so no way to tell "no bugs" from "flag ignored".
- Workaround: a Bugzilla REST quicksearch on the test file name (it also returned none).
