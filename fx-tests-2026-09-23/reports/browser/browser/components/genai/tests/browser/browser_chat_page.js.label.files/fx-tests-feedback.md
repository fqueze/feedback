# fx-tests feedback (browser_chat_page.js.label)

## Question: which of this issue's runs actually failed with the label assertion?

- Command: `fx-tests test browser/components/genai/tests/browser/browser_chat_page.js` (Issues list), and `fx-tests failures --harness mochitest --path browser/components/genai/tests/browser/browser_chat_page.js`
- Expected: the 199 FAIL runs listed under the first unexpected failure, `Chatbot menu from tab menu has label and menusepartor - false == true`.
- Got: issue 5 is keyed on `Clicked menuitem inside menupopup (state="closed") ...`, which is a TEST-KNOWN-FAIL logged in passing runs too. So the issue mixes failure modes: 2 of its 171 jobs failed with something else (`Uo6NoGoaS62A1gbOCW7pIg`: `TypeError: can't access property "length", events is null`; `EJR4dG2GQ8m1qqNfgM-JHQ`: `can't access property "disabled", ... getItemAtIndex(...) is null`).
- Workaround: `fx-tests task <id> --messages --json` for each of the 171 jobs, then a script over `failures[].allMessages`, which gave 197 label runs in 169 jobs.
- What the output could have shown: the first TEST-UNEXPECTED-* message per run as the issue key, skipping TEST-KNOWN-FAIL; or `--message <substring>` on `fx-tests test` to count and list the runs whose messages include it.
