## Network marker payload times use a different time base from everything else (review-browser_103_cleanup.js)

- Command: `profiler-cli marker info m-23 --session review-browser_103_cleanup.js-1` (YOUY4677RVy83ufVWa2OEQ, profile_browser_103_cleanup.js.json)
- Expected: the payload's `startTime`, `responseStart`, `responseEnd` and `endTime` on the same timeline as the marker's own `Time:` line and every other marker, or a label saying they are not.
- Got: `Time: 4.368s - 4.879s`, but `startTime: 4376.236625`, `endTime: 4886.618625` and `responseEnd: 4381.086458`, all 7.908 ms later (the value `profile meta` prints as "Uptime"). They are listed under "raw payload, no schema" with nothing saying they are offset. The report under review took `responseEnd` as 4.381s. The channel's `nsHttpChannel::OnStartRequest` marker is at 4.373s.
- Workaround: subtract the marker's `start` minus its payload `startTime` from every payload time.

## `thread samples` reports no samples on a thread that `thread info` says has 130

- Command: `profiler-cli thread samples --include-idle --session review-browser_103_cleanup.js-1` (the same with no flag, and in the retry profile Aq2bR-8XSDeythMZm7IFFg, 70 samples)
- Expected: the 130 samples `thread info` counts, or a reason they cannot be shown (for example, no stacks).
- Got: `Categories (0 running samples)` and `No samples in the current view.`, even with `--include-idle`, over the full range. `thread info` on the same thread gives "130 samples" and a CPU-activity breakdown.
- Workaround: took the count from `thread info`. There was no way to check the gaps between samples.

## Daemon exited without an error

- Command: `profiler-cli thread samples --include-idle --session review-browser_103_cleanup.js-2` with output discarded, then `profiler-cli status --session review-browser_103_cleanup.js-2`
- Expected: the session still running.
- Got: `Session ... is not reachable ... The daemon exited without cleaning up`. Its metadata file was gone too. The last line of its log is `Received message: command`, with no error. Reloading into a new session and running the same commands worked.
- Workaround: reloaded the profile.
