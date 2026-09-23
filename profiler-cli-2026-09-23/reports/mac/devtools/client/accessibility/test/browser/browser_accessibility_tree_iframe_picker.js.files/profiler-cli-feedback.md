## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=... profiler-cli load <taskcluster URL> --session <id>`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, followed by `Unknown session` on the next command.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call. The error message suggested it, so this cost one round trip. Falling back to `$TMPDIR` when the home directory is not writable would avoid it.

## Question: which idle-priority tasks ran on the main thread in a range?

- Command: `profiler-cli thread markers --search "Idle (0)" --list` (and `--search "priority: Idle"`), zoomed on 31.40–31.56 s, in a session where `m-42 Runnable ... IdleRequestExecutor - priority: Idle (0)` lies inside that range.
- Expected: the Runnable markers whose printed line shows `priority: Idle (0)`.
- Got: `No markers match the specified filters.` The bare term `Idle` does match them, since the priority value is `Idle` and ` (0)` exists only in the printed text, while `priority: Idle` is read as a `field:value` filter.
- Workaround: search `Idle` and ignore the extra matches, or search the runnable name (`IdleRequestExecutor`). A `--search` term copied from a printed line matching nothing, silently, is easy to misread as "none ran".

## Review: a counter bucket with no sample prints 0%

- Command: `profiler-cli counter info c-1 --session <id>` (Process CPU, parent process), zoomed on 31.43–31.56 s of UdnHrqgbRfakkLFWLJtdzg, where the counter has 5 samples about 26–32 ms apart.
- Expected: buckets without a counter sample marked as such, or buckets aligned to the sample intervals.
- Got: `[ts-YM → ts-Yp] (31.482s - 31.508s) 0%`, the bucket holding the failure, next to 88% and 76%. The JSON `graph` has the same zeros. It reads as the process being idle at the failure; there is just no sample in that bucket.
- Workaround: `--json` and read which buckets hold a sample. Printing `-` or `no sample` for such buckets would avoid the misreading.

## Review: question — how much of a range did the main thread spend running tasks, and where were the gaps?

- Command: `profiler-cli thread markers --search name:Runnable --list --limit 0 --json`, then a script merging the Runnable intervals, on 31.4998–31.5508 s of JhERgFeCTyy6B8hT9Vv_1Q.
- Wanted: whether the main thread had any idle gap an idle task could have used while an `IdleRequestExecutor` was pending. The aggregate view gives count, min/avg/max duration, but neither the covered time nor the longest gaps.
- Got from the script: Runnable markers cover 24.4 ms of 51 ms; longest gaps 4.5 ms and 4.0 ms. A `--coverage` or `--gaps` summary on `thread markers` would have answered it directly.
