## Issues block and taskIds[].message report a TEST-KNOWN-FAIL as the failure

- Command: `fx-tests test browser/components/genai/tests/browser/browser_chat_page.js` (also `--task-ids --json`, field `taskIds[].message`)
- Expected: each FAIL run's first *unexpected* failure, here `Chatbot menu from tab menu has label and menusepartor - false == true`.
- Got: `199x FAIL Clicked menuitem inside menupopup (state="closed"), which was not open...` for all 199 FAIL runs. That message is a TEST-KNOWN-FAIL (a `todo` from ClickChecks.js), logged in passing runs too. The timeout runs show it as their message as well.
- Workaround: `fx-tests task <id> --messages` on 11 sampled FAIL tasks, one per day, to find the real failure.

## Question: "which failure messages are behind this test's FAIL runs, with counts?"

- Command: loop of `fx-tests task <taskId> --messages` over sampled tasks.
- `fx-tests test` only keeps the first message per run, so the co-occurring failures (label assertion, TEST-KNOWN-FAIL todos, the pref-change known-fail) were invisible. A per-message count across the failing runs, unexpected ones only, would have answered it.

## `task --messages` does not mark expected failures

- Command: `fx-tests task JF_EeQUARVKB3SEJH-maJw.0 --messages`
- Got: `changed preference: ...` and `Clicked menuitem ...` (both TEST-KNOWN-FAIL) listed next to the real TEST-UNEXPECTED-FAIL, with nothing to tell them apart. Only loading the profile showed which was which.
