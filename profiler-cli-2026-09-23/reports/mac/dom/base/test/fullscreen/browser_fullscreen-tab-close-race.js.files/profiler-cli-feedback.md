## Marker order and ms timing within one second

- Question: in what order, to the millisecond, did ~15 markers within a 50 ms window fire (e.g. `docshell-position-size-changed`, `occlusionstatechange`, `willexitfullscreen`, `sizemodechange`), across the parent and three content processes?
- Command: `profiler-cli thread markers --search 'fullscreen,...' --list --limit 0 --session <s>` and `profiler-cli profile markers --search fullscreen --limit 0`, zoomed to `86.13,86.26`, on a 2m56s profile.
- Expected: a start time precise enough to order them (ms, or relative to the zoom start).
- Got: every row says `t=1m26s`; `marker info` also says `Time: 1m26s - 1m26s`. The list is sorted by start, but `profile markers` interleaves threads and the ordering between two rows is not visible from the times.
- Workaround: `--json` and print `start` myself, for every list in this investigation (about 12 calls).

## `profiler-cli` needs PROFILER_CLI_SESSION_DIR in this sandbox

- Command: `profiler-cli load <taskcluster url> --session <s>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a clear hint to set `PROFILER_CLI_SESSION_DIR`. Cost one call; the hint was good. `profile-link.py` honours the same variable.
