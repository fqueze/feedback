## Question: which markers were logged inside this 3 ms window (a replayed xpcshell log)?

- Command: `profiler-cli zoom push 91.552,91.555 --session test_framebindings-08.js-1` then `profiler-cli thread markers --list --limit 0 --session test_framebindings-08.js-1`
- Expected: the ~16 instant markers logged in that window (Begin/ERROR/End of the replayed log).
- Got: 1029 markers (142 KB), almost all long `test` interval markers that merely overlap the window, listed first.
- Workaround: `--json` and a script keeping `start` within the window.
- What would have answered it: a `--starts-in-range` (or `--instant`) option on `thread markers --list`, or a notice of how many listed markers only overlap the zoom.
