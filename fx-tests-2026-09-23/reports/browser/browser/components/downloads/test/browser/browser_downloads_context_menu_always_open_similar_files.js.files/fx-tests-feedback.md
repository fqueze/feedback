## `--bugs` prints nothing about bugs

- Command: `fx-tests test browser/components/downloads/test/browser/browser_downloads_context_menu_always_open_similar_files.js --bugs`
- Expected: a Bugs section (here: closed tracking bug 1909578 and older INCOMPLETE ones), or an explicit "no bug found".
- Got: the same output as without `--bugs`.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=always_open_similar_files`.

## Question: "in each failing job of my test, did test X fail first, and with which message?"

- Command: a loop over the 18 task IDs running `fx-tests task <id> --messages` and grepping for the leaker's message (`browserWin2`), a neighbour's message (`Button should exist in the palette`) and my test's two messages.
- What would have answered it: `fx-tests test <path> --task-ids` listing, per task, the other tests that failed earlier in that job (or a `--with <other test path>` co-failure count). Order dependencies are exactly this question.
