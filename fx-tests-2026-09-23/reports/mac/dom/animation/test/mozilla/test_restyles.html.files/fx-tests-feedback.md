## Question: how long after the machine booted did each crash happen? (test_restyles.html, bug 1869154)

- Command: `fx-tests crash <taskId> <minidumpId>` for each of the 29 occurrences, then `fx-tests task <taskId> --profiles`.
- Expected: the crash time (the `.extra` `CrashTime`), and something about the machine's uptime at that moment. The crash here only happens when the machine has been up for less than 100 s, which only shows once the crash time is set against the boot time.
- Got: signature, type, address, OS and stack. No crash time, no boot time.
- Workaround: curl each task's `public/test_info/<dump>.extra` for `CrashTime` and `public/test_info/system-info.log` for the `kernel_task ... created at` line (the "Boot time" line in that file is unreliable: it is quantized to 4 s, and in some tasks it comes after the harness had already started), then subtract, in a shell loop. About 29 x 2 fetches.
- What could have answered it: `fx-tests crash` printing `CrashTime` and, when system-info.log exists, the machine uptime at the crash; or a `--uptime` column in `fx-tests test --task-ids`.

## Question: which passing runs of this test on the same config can I compare against?

- Command: `fx-tests test <path> --task-ids` (failing tasks only), `fx-tests test <path> --coverage` (counts only).
- Expected: a way to get a few task IDs where the test ran and passed on the failing config, so I can read their resource-usage profiles as a control.
- Got: only failing task IDs.
- Workaround: the Treeherder jobs API (`/api/project/autoland/jobs/?job_type_name=...&last_modified__gt=...`), then loading each candidate's resource-usage profile to find out whether the test was in its chunk at all. 16 of 20 candidates did not contain the test.
- What could have answered it: `fx-tests test <path> --task-ids --passing --config <c>`, a few passing task IDs per config.
