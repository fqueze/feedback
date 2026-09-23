## Verdict says perma-fail for a failure that stopped a week earlier

- Command: `fx-tests test toolkit/components/ml/tests/browser/browser_ml_native.js`
- Expected: a verdict that accounts for `--history`: every FAIL stopped on 2026-09-12 (the fixes for bug 2068758 and bug 2071288 landed on 2026-09-10 and 2026-09-11), and only TIMEOUT remained.
- Got: `Verdict: perma-fail. Never passed on 1 configuration: test-macosx1500-aarch64/opt-mochitest-browser-chrome-cf (8/8)` — all 8 runs of that config came before the fix. It read as a current perma-fail until `--history` showed otherwise.
- Workaround: `--history`, then `--task-ids --issue 7` to find the failures that were still happening.
