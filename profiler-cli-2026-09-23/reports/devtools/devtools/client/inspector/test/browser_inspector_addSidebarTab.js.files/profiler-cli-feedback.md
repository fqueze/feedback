## Listing the tests that ran around a failure in a resource-usage profile

- Command: `profiler-cli thread markers --search "inspector/test/browser_inspector_a,inspector/test/browser_inspector_b" --list --limit 60 --session <s>` on `profile_resource-usage.json` of task Oh4bXax-Rm6iUYo9SqfqqA.
- Expected: the `test` markers (one per test, with status) for those paths.
- Got: 787 markers, mostly `output` / `console.log` / `JavaScript error` markers whose payload carries the test path; the `test` markers were drowned.
- Workaround: `--search "name:test"` plus a `zoom push` around the time; that gave the 8 `test` markers directly. A hint in `thread markers --help` (or in the guide) that resource-usage profiles are best queried with `name:test` would have saved the round trip.
- Also: `profile info` on this resource-usage profile reports "No counters" and "No significant activity", so the machine-wide CPU tracks the diagnose brief relies on were not available (or not surfaced) for this job.
