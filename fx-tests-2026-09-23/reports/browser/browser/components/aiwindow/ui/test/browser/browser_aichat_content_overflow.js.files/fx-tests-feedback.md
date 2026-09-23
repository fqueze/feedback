## `fx-tests test <path> --bugs` with no bug found says nothing about bugs

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_aichat_content_overflow.js --bugs`
- Expected: a Bugs section, or a line saying no bug names this test.
- Got: the same output as without `--bugs`; I could not tell "no bug" from "flag ignored".
- Workaround: took it as no bug.
