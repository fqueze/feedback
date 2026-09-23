## Android "Failure details not recorded" when the harness message is in the resource-usage profile

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_HeapAnalyses_takeCensus_03.js`
- Expected: the failure mode under Issues shown as `Failed to start process: ... | 0 | Could not kill left-over process`. The job's resource-usage profile has that INFO line (task Qvlqn5S8SxS70kgUU9PocQ, t=180.017 s).
- Got: `FAIL  Failure details not recorded (likely Android or platform logging issue)`.
- Cost: I had to load the profile to find out that the test never started. Grouping by that message would also show that one harness failure hits many unrelated Android tests.
