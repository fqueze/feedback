## Question: which other tests hung with the same hang-dump signature?

- Command: `fx-tests crashes --harness mochitest --signature futex --limit 0` (also `--limit 0 | grep -i futex`)
- Expected: at least this test, since `fx-tests task Mkz4RZszSKS95zxhcmuk3w --profiles` lists browser_toolbox_hosts.js as `CRASH, TIMEOUT … @ __futex_abstimed_wait_common64` (minidump 505d0e51-e682-71f9-4a87-ec7919c2a32f, taken by the harness on the 370 s no-output hang), and then other tests hanging the same way.
- Got: `No crash matched.` `fx-tests test devtools/client/framework/test/browser_toolbox_hosts.js` likewise reports `0 crash, 2 timeout`, so the hang dumps are not counted as crashes anywhere tree-wide.
- Workaround: none; could not tell whether other tests hang with the parent Renderer thread stuck in libgallium.
- What would have answered it: counting harness-triggered hang dumps (with their signature) in the crash aggregates, or flagging them as "hang dump" so `crashes --signature` can find them.

## Question: the hang profile of a 370 s no-output timeout

- Command: `fx-tests task <taskId> --profiles` on Mkz4RZszSKS95zxhcmuk3w and NXfnks5CTbGu83SOlm95cQ
- Expected: the hang profile the harness captures on "application timed out" (SIGUSR1/SIGUSR2), since it is the only per-test profile these jobs have.
- Got: `No failing test named a per-test profile in this job.` The profiles exist as `public/test_info/profile_0_13014.json` and `public/test_info/profile_0_1580.json`, visible only as `artifact` markers in the resource-usage profile.
- Workaround: listing the task's artifacts with the Taskcluster API.
- What would have answered it: listing `profile_0_<pid>.json` hang profiles in `task --profiles` next to the resource-usage profile.

