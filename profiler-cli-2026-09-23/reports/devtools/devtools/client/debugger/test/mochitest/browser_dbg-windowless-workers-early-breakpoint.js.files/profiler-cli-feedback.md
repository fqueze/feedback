# profiler-cli feedback

## Question: "in what order, and how far apart, did these test log steps happen?"

- Command: `profiler-cli thread markers --category Test --search windowless-workers-early-breakpoint --list --limit 0 --session <s>`
- Expected: timestamps precise enough to order and space the steps (ms).
- Got: `t=1m45s` for ~40 consecutive markers spanning 104.0s-105.4s, so the one-second rounding hides both order and the gaps between steps (e.g. the 500 ms between two TEST-PASS lines).
- Workaround: re-ran with `--json` and printed `flatMarkers[].start`.
- Could have shown: `t=1m45.353s` (ms precision) in `--list` mode, at least when the listed range is short.

## Question: "zoom to 104.0 s - 150.0 s" (given in ms by mistake)

- Command: `profiler-cli zoom push 104000,150000 --session <s>` on a 2m30s profile
- Expected: an error, since both ends are past the profile's end (150 s).
- Got: accepted silently; `status` showed `View range: ts>10g to ts>10y`, the prompt showed `View: ... (12h47m)`, and the next `profile markers --search` said "No markers match", which reads like a real negative.
- Workaround: `zoom clear`, then `zoom push 104.0,150.0`.
- Could have shown: "range 104000 s-150000 s is outside the profile (0-150 s); times are in seconds".

## Reviewer (review-browser_dbg-windowless-workers-early-breakpoint.js)

### Daemon died mid-session with no reason given

- Command: `profiler-cli marker info m-161 ... --json --session review-wwe-1`, a few minutes after loading, after ~30 successful queries.
- Expected: the answer, or a reason the daemon stopped.
- Got: `Session review-wwe-1 is not reachable. ... The daemon exited without cleaning up.`, then `Unknown session` on the next call. The log's last line is `Received message: command`, with no error or exit reason. Nothing in the kernel log.
- Workaround: loaded the profile again (another ~0.6 GB and another download).
- Could have shown: why the daemon exited (signal, OOM, exception) in the log and in the error.

### Question: "was this thread sampled near t, the moment it woke up?"

- Command: `profiler-cli zoom push 105.354,105.356` then `thread samples --include-idle`, and narrowing the zoom repeatedly.
- Expected: the timestamps of individual samples in a short range.
- Got: only aggregate counts, so I found out whether a sample landed in a 2 ms window by zooming in step by step.
- Could have shown: a `thread samples --list` mode (one row per sample: time, and leaf or category) for a short zoom.
