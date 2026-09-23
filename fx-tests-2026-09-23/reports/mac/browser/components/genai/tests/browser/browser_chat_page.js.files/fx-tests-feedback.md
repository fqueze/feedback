# fx-tests feedback (browser_chat_page.js)

## Issues and --task-ids label a FAIL run with a TEST-KNOWN-FAIL message

- Command: `fx-tests test browser/components/genai/tests/browser/browser_chat_page.js`
- Expected: the FAIL row of "Issues (first failure per run)" to carry the first
  TEST-UNEXPECTED-FAIL message of the run.
- Got: `215x FAIL Clicked menuitem inside menupopup (state="closed"), which was not open. ...`.
  That message is a `SimpleTest.todo` from ClickChecks.js: it is logged as TEST-KNOWN-FAIL in
  every run of this test, passing ones included (profile of task K35tD4uLRTeTIrb59qDbUg shows it
  as TEST-KNOWN-FAIL). The real failure in those runs is
  `Chatbot menu from tab menu has label and menusepartor - false == true`, which appears nowhere
  in `fx-tests test` output. The same happens in `fx-tests task --messages`, where known-fail
  messages are listed next to the unexpected ones without any distinction, and the default
  `fx-tests task` output leads with the todo message.
- Workaround: `fx-tests task <id> --messages` on a few tasks, then reading the profile's
  TEST-UNEXPECTED-FAIL markers.
- Question it could have answered: "what is the failure message of the FAIL runs?" — skip
  TEST-KNOWN-FAIL / todo messages when choosing a run's first failure, or mark them as such.
