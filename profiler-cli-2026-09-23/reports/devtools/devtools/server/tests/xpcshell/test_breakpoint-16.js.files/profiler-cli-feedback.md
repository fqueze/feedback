## Question: "which log lines were emitted at this moment" (the replayed full log of one xpcshell test)

- Command: `profiler-cli zoom push 91.4285,91.4296 --session test_breakpoint-16.js-1` then `profiler-cli thread markers --session test_breakpoint-16.js-1 --list --limit 0`
- Expected: the ~12 instant markers (Begin/End of full log, ERROR, `xpcshell return code`) that fall in that 1.1 ms window.
- Got: 1056 markers, because every long `test` interval marker overlapping the window is listed too (the whole parallel batch of 45 s TIMEOUT markers, plus `Phase`/`parallel`), and they sort first.
- Workaround: `--json` and a script keeping only markers with no duration.
- What could have answered it: an option to list only markers that start inside the zoom (or only instant markers), e.g. `--starts-in-view` / `--instant`.
