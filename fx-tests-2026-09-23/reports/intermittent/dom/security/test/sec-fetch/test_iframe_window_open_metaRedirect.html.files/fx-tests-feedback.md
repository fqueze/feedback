## Question: where is the runtime evidence for an Android failure?

- Command: `fx-tests task TsAUSkoPRfOkzEgKV-3_Ow --profiles` (and 5 other android-em-14 mochitest-plain tasks).
- Expected: either a per-test profile, or a hint of what to read instead.
- Got: `No failing test named a per-test profile in this job.` for every Android task — Android jobs never upload per-test profiles, which is not said.
- Workaround: listed the task artifacts by hand and read `public/test_info/logcat-emulator-5554.log`, which has the parent and content process Gecko output (console errors, GeckoView navigation events) interleaved with the test log.
- Could have shown: "Android jobs upload no per-test profile; the logcat artifact is <url>" in place of the generic line.
