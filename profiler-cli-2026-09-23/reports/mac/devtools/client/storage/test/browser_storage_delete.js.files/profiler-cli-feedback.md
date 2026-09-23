## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=browser_storage_delete.js profiler-cli load "<taskcluster url>" --session browser_storage_delete.js-1`
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The message did suggest `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`, which worked.
- Workaround: export `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` before every call. Cost one extra round trip. The subagent brief does not mention it, so each agent finds out on its own.

## Question: "which of these markers, a fraction of a millisecond apart, came first?"

- Command: `profiler-cli thread markers --search "debounce,getStoreObjects,getFields,single-store,…" --list --limit 0 --session …`
- The race here is decided by sub-millisecond ordering (a stray reply 0.12 ms before the assertion; two timers in two processes 22 µs apart). The `--list` output prints `t=33.546s`, so a dozen markers all read `t=33.546s` and their order cannot be read. I had to pipe `marker info m-… --json` through a script to print `start`/`end` in ms.
- What would have answered it: an option to print start (and end) in ms with sub-ms precision in `--list`, e.g. `--time-precision us`, or automatically adding decimals when consecutive rows share the same rounded time.
- Also: in `thread markers --list --json`, `flatMarkers[].end` came out `null` for interval markers such as `RDP Front` (duration shown in the text output), so a second call to `marker info` was needed to get end times.

## Reviewer (review-browser_storage_delete.js): the same two costs again

- Default session directory: the first `profiler-cli load` failed with the same `EPERM` on `/Users/florian/.profiler-cli`, and needed `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli`. Neither `review-brief.md` nor `subagent-brief.md` mentions it, so every agent pays one round trip.
- Question: "did any timer runnable run on t-0 between 34.336 s and 34.558 s, and which markers came in what order within 34.5565–34.563 s?" — `thread markers --search Runnable --list` prints `t=34.415s` for rows 30 µs apart, so again I read them through `--list --json` and a script printing `start` in ms. A `--time-precision us` (or ms with 3 decimals) on `--list` would have answered it directly.
