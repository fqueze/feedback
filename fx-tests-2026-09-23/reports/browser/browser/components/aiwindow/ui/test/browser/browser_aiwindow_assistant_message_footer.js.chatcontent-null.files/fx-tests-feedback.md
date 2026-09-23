## `fx-tests test <path> --bugs` prints nothing about bugs when there are none (same as in the sibling report's feedback)

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_aiwindow_assistant_message_footer.js --bugs`
- Expected: a "Bugs" section, or "no bug names this test".
- Got: output identical to the run without `--bugs`.
- Workaround: Bugzilla REST quicksearch on the file name (none).
