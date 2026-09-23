## Question: "a profile holding a passing run of this test" (to compare with the failing ones)

- Commands: a loop of `fx-tests test dom/events/test/<each later test in the manifest> --config windows11-64-25h2-ccov,macosx1500-aarch64/debug`, then `fx-tests task <taskId> --json` to see if my test ran in that job.
- Expected: one command listing jobs where this test PASSED and a later test of the same manifest failed on the same config (so its per-test profile covers my test's passing run).
- Got: 44 invocations to find 2 candidates, both on `-xorig` where my test is SKIP; no usable comparison found.
- What could have shown it: `fx-tests test <path> --passing-profiles` (per-test profiles of later failures in the same manifest/job where this test passed).

