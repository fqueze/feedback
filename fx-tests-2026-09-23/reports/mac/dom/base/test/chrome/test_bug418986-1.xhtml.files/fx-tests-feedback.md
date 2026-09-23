## `fx-tests task` refuses jobs killed at max-run-time, though their per-test profiles were uploaded

- Command: `fx-tests task M84Z3WoXQiqaB5w4ULDPEQ --profiles` (also QVroiDtfQn6CDTendUZN9g, Cf0CwPXyTTKtgSj92tYVdA, VQzg0BAsTtiYtTKf28quBg, G-89BzTDSHusL4WKCbzOhA)
- Expected: the list of failing tests and their per-test profile URLs, even if the resource-usage profile is a partial stream.
- Got: "task ... was killed for exceeding its maximum duration ... The job never got to write a profile, so there are no per-test results to read." But the task's artifact list has `public/test_info/profile_test_bug418986-1.xhtml.json`, `-2`, and per-test profiles for 6 other tests, plus `mochitest-chrome_errorsummary.log`.
- Workaround: `curl .../task/<id>/runs/0/artifacts | jq '.artifacts[].name'` and load the per-test profile URLs by hand.
- Related: `fx-tests test` misses these jobs entirely: it reports 0 failures in 376 runs of `test-macosx1500-aarch64-vms/opt-mochitest-chrome-1proc`, while sheriffs starred 19 such jobs on bug 2073837 in 2 days. The verdict ("fails on 1 configuration ... -cf at 75%") hides that the main config fails almost every time. The question it could not answer: "is the main config failing too, in jobs that timed out?"
