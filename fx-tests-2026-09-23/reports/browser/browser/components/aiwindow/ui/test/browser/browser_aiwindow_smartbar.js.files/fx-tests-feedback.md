## Question: which tests are behind one crash signature?

- Command: `fx-tests crashes --harness mochitest --path browser/components/aiwindow/ --signature mozalloc_handle_oom --limit 0 --full-messages`
- Expected: the 23 tests behind the row, with counts (the text output says `tests 23`).
- Got: only the aggregate row (`221  23  0  @ mozalloc_handle_oom`), with no way to list the tests in text mode.
- Workaround: `--json` has `rows[].tests[]` with per-test counts; I read it with a python one-liner.
- What would have answered it: a `--tests` flag, or printing the per-test breakdown under a row when `--signature` narrows to one signature.

## Question: is the OOM crash really behind this test's DbgHelpWrapper crashes?

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_aiwindow_smartbar.js`
- Expected: some hint that `@ DbgHelpWrapper::Initialize::<lambda_1>::operator()` is an OOM crash (the crash reason is `out of memory: 0x101000 bytes requested`; the DbgHelp frame is only the stack walker failing in the crash handler).
- Got: the issue list shows 7x `CRASH @ DbgHelpWrapper::Initialize...` as its own mode; only `fx-tests crash <task> <dump>` shows `mozalloc_handle_oom` further down the stack.
- What would have answered it: showing the Mozilla crash reason next to the signature in `test`'s Issues list, or skipping mozglue StackWalk/DbgHelp frames when picking the signature.
