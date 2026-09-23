## profiler-cli 0.9.0: `zoom push` silently misreads the tool's own `XmYs` time format (review-browser_console_screenshot.js)

- Command: `profiler-cli zoom push 19m50s,23m50s --session <s>`, after `thread markers --list` had printed times like `t=19m58s`.
- Expected: a zoom to 19m50s to 23m50s, or an error saying the format is not accepted.
- Got: `Pushed view range: ts-1 (19s) to ts-2 (23s) (duration: 4s)`, with no error. The following `--search` then returned no markers, which looks like "no such marker", not "wrong range".
- Workaround: pass seconds (`zoom push 1190,1440`).
