## Question: at what time did a given JS function run?

- Command: `profiler-cli thread functions --search openNative --session <s>` then `thread samples-top-down` under successive `zoom push` windows.
- Expected: a way to list the sample times (or first/last sample time) of a function handle, e.g. `profiler-cli function info f-919` showing when its samples occur.
- Got: counts only; I had to bisect with five zoom windows to find that `openNative` ran at 41.90–41.96 s.
- Workaround: bisecting with `zoom push`/`zoom pop`.
