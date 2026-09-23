## `--bugs` gives no answer when nothing open matches

- Command: `fx-tests test devtools/client/inspector/rules/test/browser_rules_highlight-property.js --bugs`
- Expected: a Bugs section, even an empty one ("no open bug names this test"), ideally with closed ones listed too.
- Got: the same output as without `--bugs`, and no Bugs section at all. I could not tell "none found" from "flag ignored".
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=browser_rules_highlight-property'`, which found the tracking bug 1884491 (RESOLVED INCOMPLETE 2026-06-29) and three older closed ones.

## Question: which other tests fail in the same jobs as this one?

- Command: a shell loop running `fx-tests task <id>` over all 11 task IDs from `fx-tests test <path> --task-ids --limit 0`, then grepping the FAILED lists.
- What the output could have shown: `fx-tests test <path>` could list the tests that co-fail in this test's failing jobs, with counts ("browser_rules_inherited-element-backed-pseudo-elements.js: 11 of 11 jobs"). Here that was the most striking fact about the failure. It took 11 task fetches to find, and a triager would miss it without the loop.
