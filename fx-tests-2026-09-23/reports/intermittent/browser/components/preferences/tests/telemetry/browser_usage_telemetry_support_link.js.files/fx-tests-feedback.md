## Question: "which add_task timed out, in each failing run?"

- Command: `fx-tests test browser/components/preferences/tests/telemetry/browser_usage_telemetry_support_link.js --task-ids --limit 0 --issue 1`
- Expected: the subtest (add_task name) that was running at the timeout, per run or aggregated (the sheriff annotation on bug 2040565 had it: "testSupportLinkTelemetry - Test timed out" vs "testSupportLinkWithIdOverride - Test timed out").
- Got: every run collapses into "TIMEOUT Test exceeded time limit"; which task hung (first task vs second task, which changes the diagnosis: pass-then-fail inside one browser is the best comparison) is only found by loading each per-test profile.
- Workaround: loaded profiles one at a time until one showed the first task passing and the second failing (V4pcjMdOTgyhn_Vm0zzPJQ `-2`).
