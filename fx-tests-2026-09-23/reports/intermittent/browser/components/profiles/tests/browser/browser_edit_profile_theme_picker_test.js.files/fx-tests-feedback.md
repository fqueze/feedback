## Question: the JS stack of a test's uncaught rejection (review-browser_edit_profile_theme_picker_test.js)

- Command: `fx-tests task Mt4Kl2wCRPWokQw9I4iTVg --profiles`
- Expected: the failure's message with the stack lines that follow it in the log. For `uncaught rejection: …` they are the rejection's async stack, which showed which caller of `updateProfile` failed.
- Got: the message line only. The profile's `TEST-UNEXPECTED-FAIL` marker has no stack either, only the harness's own capture stack.
- Workaround: `curl …/artifacts/public/logs/live_backing.log`, then `rg -A30 "uncaught rejection"`.
