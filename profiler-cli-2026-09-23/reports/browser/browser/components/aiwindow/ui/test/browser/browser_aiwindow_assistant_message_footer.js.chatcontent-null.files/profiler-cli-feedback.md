## IPC markers in `thread markers --list` show no message type (same as in the sibling report's feedback)

- Question: "which queued IPC message, delivered late to a cold content process, is the `LoadURL` and which is the test's `Spawn`, and in what order were they sent?"
- Command: `profiler-cli thread markers --session S --search "-name:Preference Read,..." --list --limit 400` on the Privileged Content main thread.
- Expected: `IPCIn` rows with `PBrowser::Msg_LoadURL`, `PWindowGlobal::Msg_RawMessage` and the seqno.
- Got: 150 rows reading only `IPCIn`, with an empty description.
- Workaround: `--search "messageType:PBrowser::Msg_LoadURL,messageType:PWindowGlobal::Msg_RawMessage" --json` and a script printing `messageType`, `messageSeqno`, `start`. The `messageType:` search did work, which is how I found them.

## `profile info` "CPU activity over time" prints CPU-milliseconds as if they were a duration

- Command: `profiler-cli profile info --all --session S`
- Expected: `410% for 3.12s: [ts-R → ts-W] (10.364s - 13.483s)`.
- Got: `410% for 12798.4ms: [ts-R → ts-W] (10.364s - 13.483s)`. 12.8 s is 410% of the 3.1 s range, so it reads as if the range lasted 12.8 s. It also counted CPU for processes that have no samples yet (their Gecko profiler starts after a 2.7 s `xul.dll` load), and that 410% did not match the per-thread CPU totals listed just above it (about 3.8 s for the whole profile).
- Workaround: checked the resource-usage profile's `CPU Use` markers instead.

## Machine CPU per test in a resource-usage profile needs a script

- Question: "was the system-CPU spike during the failing test unusual for this job, or does every test start look like that?"
- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` and `--search "name:test" ... --json` on the resource-usage profile.
- Expected: something like `thread markers --search "name:test" --with-counter-stats "CPU Use"`, giving each test marker the mean and max of `cpuPercent` / `system_pct` over its duration.
- Got: 12,654 `CPU Use` markers, each only readable one by one.
- Workaround: a python script joining the two JSON dumps by time (`ru1-*.json` in this directory). It showed the failing test is the only one of 178 in the job with a mean system CPU above 15%.
