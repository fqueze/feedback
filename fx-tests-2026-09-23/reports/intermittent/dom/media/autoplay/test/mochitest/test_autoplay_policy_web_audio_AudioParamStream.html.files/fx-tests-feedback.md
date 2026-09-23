# fx-tests feedback (test_autoplay_policy_web_audio_AudioParamStream.html)

## Question: which push is each failure on, so I can find the landing that started or stopped a step change?
- Command: `fx-tests test <path> --task-ids --limit 0` (also `--json`)
- Expected: the revision (and push time) next to each task ID, since `--history` shows a step change and the next question is always "what landed at its edges".
- Got: task ID, job name and day only. `taskIds[]` in `--json` has no revision either.
- Workaround: 18 `curl .../api/queue/v1/task/<id>` calls to read `GECKO_HEAD_REV` and `created`, then Treeherder's push API for push IDs.

## Question: what is the minidump ID of the crash in this job?
- Command: `fx-tests task <taskId> --profiles`, and `fx-tests test <path> --task-ids`
- Expected: the minidump ID next to "CRASH, TIMEOUT" (the `test --help` says `--task-ids` prints minidump IDs of crashes), so it can go straight into `fx-tests crash`.
- Got: `task` prints "CRASH, TIMEOUT — 2 failing executions of 2 ... @ libc.so + 0x...e5588" with no minidump ID. `test` counts these runs as TIMEOUT (0 crash), so `--task-ids` prints no minidump ID either.
- Workaround: downloaded live_backing.log and grepped `PROCESS-CRASH | <id>`.
- Why it matters: on Android "application timed out ... with no output" hangs, the parent minidump is the only stack there is. Here it held the answer (AudioIPC Server thread stuck in cubeb_aaudio).

## Question: what did the device log while the app hung?
- Command: `fx-tests task <taskId> --profiles`
- Expected: for Android hardware jobs, a pointer to `public/test_info/logcat-*.log` (and `trace_00`). No per-test profile exists for an app-timeout, and the resource-usage profile only has the host's CPU.
- Got: "No failing test named a per-test profile in this job", and nothing else.
- Workaround: listed the task's artifacts by hand.

## `fx-tests crash --all-threads` leaves some Android libxul frames unsymbolicated
- Command: `fx-tests crash L2sOx7XDQcmcGPh0o7kw3g 66046fd7-a11e-08a9-5129-551fee478e22 --all-threads`
- Expected: `libxul.so!...` frames, as for `fx-tests crash OPDhUhkaQQKMl78oPQQ3sw 7bdd6b17-...`.
- Got: `base.apk + 0x14704e44` for every libxul frame. 6 of the 18 jobs' dumps are like this in the job log as well, so the tool may just be inheriting it.
- Workaround: compared the libaaudio frame offset (`libaaudio.so + 0x11c20`) with the symbolicated dumps.
