# fx-tests feedback (browser_chat_page.js.menu-label)

## The failure mode I was given does not appear under Issues; a TEST-KNOWN-FAIL takes its row

Question: how many runs failed with `Chatbot menu from tab menu has label and menusepartor - false == true`?

Command: `fx-tests test browser/components/genai/tests/browser/browser_chat_page.js`

Expected: an Issues row for that message (it is the only TEST-UNEXPECTED-FAIL in these FAIL runs).

Got: `199x FAIL Clicked menuitem inside menupopup (state="closed"), which was not open...`. In the
profiles of those runs (e.g. Pr1dllrVTaSllHhb9B-KpQ, DXFMldBmTk-IRtyorvwY8A `-2`), that message is a
`TEST-KNOWN-FAIL` (a `SimpleTest.todo` from ClickChecks.js), logged before the real failure. The real
failure message is nowhere in the Issues block, so a reader would diagnose the wrong thing.

Workaround: `fx-tests task <id> --messages` on ~20 jobs of that row to confirm they all carry the
`Chatbot menu` message, then used the row's count as a proxy.

Same root in `fx-tests task --messages`: known-fails (`Clicked menuitem ...`, `handleEvent() was
unable to perform a11y checks ...`, `changed preference: ...`) are listed next to the real failure
with no status column, so the failure cannot be told from the noise without opening the profile.
Showing the status (TEST-UNEXPECTED-FAIL vs TEST-KNOWN-FAIL) per message, and skipping known-fails
when choosing the "first failure per run", would have answered this directly.
