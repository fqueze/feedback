## `fx-tests task` / `fx-tests try --profiles` miss per-test profiles of CRASH failures

- Command: `fx-tests task GQYf3gDHRlep__mE302Jmg --profiles` (and `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids --limit 0`)
- Expected: the per-test profile URLs for `test_2_conformance__misc__uninitialized-test.html`, which crashed (CRASH status).
- Got: "No failing test named a per-test profile in this job." and `testProfiles: []` in `--json`; the try view lists only the resource-usage profiles.
- But the resource-usage profile has `FAIL` markers `profile uploaded in profile_test_2_conformance__misc__uninitialized-test.html.json` and `profile uploaded in profile_test_2_conformance__misc__uninitialized-test-2.html.json` (and matching `artifact` markers, 8.67 MB and 2.70 MB). These FAIL markers are attached to the test but come after the CRASH, so the tool presumably only reads the message of the failure it picked.
- Workaround: `profiler-cli thread markers --search <test file> --list` on the resource-usage profile, then build the artifact URL from the name.
- Also: the minidump ID (`EA19BCBD-...` in the CRASH marker) is not in `fx-tests task --json` either, so `fx-tests crash` cannot be reached from it.
