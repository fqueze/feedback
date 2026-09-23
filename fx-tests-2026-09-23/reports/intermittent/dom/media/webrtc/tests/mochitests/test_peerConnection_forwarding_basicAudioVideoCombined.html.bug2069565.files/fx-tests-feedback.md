## `fx-tests task` / `fx-tests test` undercount a timed-out test when its `test` marker is missing

- Command: `fx-tests task drKYLokVQTaL9ymgfYwqJQ.0`, and `fx-tests test dom/media/webrtc/tests/mochitests/test_peerConnection_localRollback.html`
- Expected: 4 failing tests in that job (forwarding_basicAudioVideoCombined, insertDTMF, localReofferRollback, localRollback); localRollback counted as a timeout in the same 42 jobs as its three neighbours.
- Got: "3 failing" for that job; localRollback shows 17 timeouts tree-wide while the three tests before it in the same cascade show 42. The job's resource-usage profile does have `FAIL ... test_peerConnection_localRollback.html Test timed out.` (m-485) and `4 test timeouts, giving up.` (m-486), but it has no `test` marker for localRollback, which seems to be what fx-tests counts. The 25 jobs missing are the older ones (2026-09-04 … 09-14).
- Workaround: listed the job's `FAIL` markers with profiler-cli.

## Android jobs: the only Gecko-side evidence is the logcat, and nothing points to it

- Command: `fx-tests task XKJvejU0QwiGAyXzwRU-WA.0 --profiles`
- Expected: some pointer to where evidence from inside the browser lives, when no per-test profile exists.
- Got: "No failing test named a per-test profile in this job." That is correct, but the resource-usage profile holds only the harness (one `mach` thread). What settled this failure was `public/test_info/logcat-emulator-5554.log`, which I found by listing the task's artifacts by hand.
- Suggestion: on Android jobs, print the logcat artifact URL next to the resource-usage profile.
