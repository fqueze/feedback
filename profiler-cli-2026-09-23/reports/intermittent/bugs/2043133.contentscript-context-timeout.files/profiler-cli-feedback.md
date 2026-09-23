## Daemon dies with SIGABRT, and no reason, loading a large profile

- Command: `profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/fBksrlxAThGrBQb688OEzw/runs/0/artifacts/public/test_info/profile_test_ext_contentscript_context.js.json" --session 2043133.contentscript-context-timeout-3` (the artifact is 881 MB gzipped; its parent main thread holds millions of `DummyEvent` Runnable markers).
- Expected: the profile loads, or an error naming the cause (out of heap, too many markers).
- Got: `Error: The profiler-cli daemon died while loading the profile (killed by signal SIGABRT).` followed by `Last lines of ...log:` with nothing after it. A first attempt with the same command printed only daemon connect/disconnect log lines and exited, leaving a stale session. 30 GB of RAM were free.
- Workaround: read another failing task's profile instead (OOJ4POhnR0K6rzHfgV10VQ, 426 MB, loaded fine).

## Question: "which IPC messages went to, and came from, pid N after time T?"

- Command: `profiler-cli thread markers --category IPC --list --limit 0 --session 2043133.contentscript-context-timeout-1`
- Expected: rows showing the message type, the direction, the peer pid and the seqno.
- Got: rows reading `IPCOut` / `IPCIn` and a time. I had to use `--json` and a script printing `data.messageType`, `data.otherPid`, `data.messageSeqno` to find that the bfcached process never sent anything after its priority was lowered. `--search` cannot filter by `otherPid` either: I had to compare `str(otherPid)` in the script. (The 2043133 report logged the same thing.)

## Question: "what was the machine's CPU use while this test ran?"

- Command: `profiler-cli counter list --session ...-ru1` on a resource-usage profile says `No counters in this profile`. The CPU is only in the `CPU Use` interval markers, one every 100 ms.
- Expected: a CPU summary for a zoom range, like `counter info` gives for a per-test profile.
- Got: one marker per 100 ms. I used `--json` and a script averaging `data.cpuPercent` (a string such as `"100.0%"`) per minute and over the test's own range.
