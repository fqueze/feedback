## `Issues` lists a TEST-KNOWN-FAIL as a FAIL failure mode

- Command: `fx-tests test browser/components/genai/tests/browser/browser_chat_contextmenu.js`
- Expected: failure modes made of unexpected results only.
- Got: `4. 1x FAIL Clicked menuitem inside menupopup (state="closed"), which was not open...`. That run (task YZGJ9wl9TVWs7So9HHpGlg, linux asan) is a `Test timed out`; its profile logs the "Clicked menuitem" message as `TEST-KNOWN-FAIL` (it is emitted as known-fail in passing runs too). `fx-tests task YZGJ9wl9TVWs7So9HHpGlg --messages` lists it first among the failing messages, which is presumably why it became the "first failure".
- Workaround: loaded the profile to see the TEST-KNOWN-FAIL status. Cost: one profile load, and a false second failure mode.
