## `fx-tests task` says "Passed when the harness reran it" when the retry failed too

- Command: `fx-tests task cOrphxTIT4ehoo6XfZEgcA --profiles --messages` (same on QNNvRn7bR7SNi_MPD3rjJA, Twh8y7n7SeGfee6KbkDwsQ, YbUKuFkITlmYK-ZYvcWjOg)
- Expected: the retry reported as failed. The job has a `profile_browser_bug503832-2.js.json`, i.e. the retry failed, and the resource-usage profile shows the retry's run list as PASS then FAIL (timeout), same as the first run.
- Got: "FAIL — 2 failing executions of 4 / Passed when the harness reran it."
- Likely cause: in a test-verify job the test runs N times per browser; the retry's first iteration passing is read as "the rerun passed".
- Workaround: read the `test` markers in the resource-usage profile.
