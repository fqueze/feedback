## Question: were samples taken at the configured interval?

- Command: `profiler-cli profile meta` says `Sampling interval: 10ms`; I only found out the real spacing was 47-61 ms by `zoom push 20,60` then `thread samples --include-idle` (656 samples in 40 s) on `profile_browser_fontinspector_editor-variable.js.json` of task P2GCPtPfQVGhaGBUJ_us4g.
- Expected: `profile info` or `profile meta` to report the effective sample spacing per thread (median gap between samples) next to the configured one, and flag a large mismatch. On these Mac 15.30 profiles it is itself evidence (late thread wakeups) and it changes how much a "no sample in this 50 ms" means.
- Workaround: count samples in a zoomed idle range and divide.

## Sandbox: default session dir not writable

- Command: `profiler-cli load <url> --session ...` without `PROFILER_CLI_SESSION_DIR`.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`; the message did say what to set, so this cost one call.

## Question (review): which of two timers firing in the same millisecond sent this request?

- Command: `profiler-cli thread markers --list --search debounce,pagestyle` with `zoom push 13.540,13.900` on the P2GC profile (task P2GCPtPfQVGhaGBUJ_us4g). It shows two `setTimeout() for exports.debounce…[debounce.js]` runnables and `pagestyle:getUsedFontFaces()` all at `t=13.895s`.
- Expected: the list shows sub-millisecond start times when rows share a millisecond, or marks a marker that starts inside another's interval (for example, the request nested in the runnable that sent it).
- Got: millisecond times only. I needed `marker info m-653 m-654 m-655 --json` to see that the request starts at 13894.995, inside the second runnable (13894.965 to 13895.052). The original report had linked the first runnable.
