# fx-tests feedback — browser_policy_search_engine.js

## `--bugs` prints nothing about bugs

- Command: `fx-tests test browser/components/enterprisepolicies/tests/browser/browser_policy_search_engine.js --bugs`
- Expected: a "Bugs" section — the bugs naming the test, or an explicit "none found".
- Got: exactly the same output as without `--bugs`; no line about bugs at all, so "no bug" and "flag ignored" look the same.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=browser_policy_search_engine`, which found the single tracking bug 1935589 (RESOLVED FIXED in 2026-01) and three older closed ones.
