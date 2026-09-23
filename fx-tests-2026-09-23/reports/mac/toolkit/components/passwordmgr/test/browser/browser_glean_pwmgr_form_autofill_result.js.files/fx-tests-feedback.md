## `--bugs` prints nothing about bugs

- Command: `fx-tests test toolkit/components/passwordmgr/test/browser/browser_glean_pwmgr_form_autofill_result.js --bugs`
- Expected: a Bugs section listing bug 1873014 (whose summary names the test), or an explicit "no bug names this test".
- Got: the same output as without `--bugs`; no Bugs section and no "none found" line, so absence and failure to look are indistinguishable.
- Workaround: `fx-tests intermittent --bug 1873014 --since 30 --tree all` (the default 7-day window found no annotation and exited 2).
