## Issues block reports a TEST-KNOWN-FAIL (todo) as the "first failure" of a FAIL run

- Command: `fx-tests test browser/components/genai/tests/browser/browser_chat_page.js`
- Expected: issue row 5 names the assertion that actually failed in those runs, i.e.
  `Chatbot menu from tab menu has label and menusepartor - false == true`.
- Got: `199x FAIL Clicked menuitem inside menupopup (state="closed"), which was not open...`. In the
  per-test profiles that message is a `TEST-KNOWN-FAIL` (a `SimpleTest.todo` from ClickChecks.js),
  emitted early in every run, passing ones included. The real `TEST-UNEXPECTED-FAIL` comes later
  and never shows up in the Issues block.
  `fx-tests task <id> --messages` has the same problem: it lists `2x Clicked menuitem...` and
  `2x handleEvent() was unable to perform a11y checks...` (both todo) at the top, with nothing to
  mark them as expected failures.
- Cost: I thought there was a separate "clicked a closed menupopup" failure mode, and only found
  the real assertion after loading the retry profile.
- Workaround: read `thread markers --category Test --list` in the profile and look for
  `TEST-UNEXPECTED-FAIL`.
- Suggestion: skip TEST-KNOWN-FAIL / todo messages when choosing a run's first failure, or at
  least tag them as such in `task --messages`.
