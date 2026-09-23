## Empty call tree with no word about the range holding no samples

- Command: `profiler-cli thread samples-top-down --include-idle --max-lines 120 --session <s>` with a zoom of 4.750,4.805 (and `zoom push m-96`, a 23 ms runnable) on a CI per-test profile whose parent main thread has 121 samples over 5.5 s.
- Expected: "0 samples in this range" (and ideally the thread's sample count/effective interval, since `profile meta` says 10 ms but the thread has one sample every ~45 ms).
- Got: the header, then `Top-Down Call Tree:` and nothing. Looked like a filter or zoom problem until `thread info` showed the sample count.
- Workaround: `thread info` to get the sample count, then relied on markers with stacks.

## Default session directory not writable in a sandbox

- Command: `profiler-cli load <taskcluster url> --session <s>`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`, with a clear hint to set `PROFILER_CLI_SESSION_DIR`. The hint worked; noted only because every agent sharing the machine pays one failed load for it.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command, including `profile-link.py`.
