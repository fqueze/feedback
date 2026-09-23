## `fx-tests test` verdict says "perma-fail" for a failure that stopped a week ago

- Command: `fx-tests test browser/components/urlbar/tests/unit/test_keywords.js`
- Expected: a verdict that says the failure stopped (the `--history` shows 0 failures from 2026-09-16 to 2026-09-21, after 194 failures from 09-11 to 09-15), or one that says "was perma-failing on shippable configs from 09-11 to 09-15".
- Got: `Verdict: perma-fail. Never passed on 1 configuration: test-macosx1015-64-shippable-qr/opt-xpcshell-cf (10/10)`. That config seems to have run only during the failing window. The per-config table shows 24-29% on every shippable config, which hides that they were 100% for four days and 0% before and after.
- Workaround: read `--history` and map the failing revisions to the landing that stopped the failure by hand.
