## (review) `profile info` shows full-profile thread CPU under a zoomed header

- Command: `profiler-cli zoom push 1476.58,1567.39 --session S` then `profiler-cli profile info --session S`
- Expected: per-thread CPU for the 91s view, as the header `View: ts-X→ts-z (1m31s)` says.
- Got: `t-0: GeckoMain - 408749.671ms`, the whole 26-minute profile's CPU: more than the 91s view could hold. `thread info` does honour the zoom ("91% for 82326.8ms").
- Workaround: `thread info`. Fix: honour the zoom, or label the figures as full-profile.

## (review) Question: "what share of samples is in one native library (ntdll's unwinder)?"

- Command: `profiler-cli thread samples` / `thread functions --search "ntdll.dll!Rtl"` on a Windows ASan CI profile.
- Got: every native frame has total == self, so there's no single frame to read the unwinder's share from. It took summing 10 functions by hand (15.6 + 15.2 + 4.9 + 4.4 + ...) to get 41%. The earlier report quoted only one of them (15.6%).
- Could show: a per-library rollup of self time (ntdll.dll, clang_rt.asan_dynamic, xul.dll) in `thread samples`.

## (review) Questions answered only with a script over `--json`

- "How long did each add_task take, and how long between two log lines inside each?": `thread markers --category Test --search <file> --list --json`. A duration between paired `Entering test`/`Leaving test` markers would answer it.
- "How does the CC graph size change over the test?": `thread markers --search name:CC --list` shows no payload fields. A `--fields mVisitedGCed,mFreedGCed` column option would answer it.
- "Mean/median machine CPU over a range" in a resource-usage profile: `thread markers --search "name:CPU Use" --list --json` and averaging `cpuPercent`. A summary for CPU Use markers in the zoomed range would answer it.
- "Which windows created during this test outlived it?": `thread markers --search name:DOMWindow --list --json`, grouped by `test` markers. Showing the enclosing `test` marker next to each marker would answer it.
