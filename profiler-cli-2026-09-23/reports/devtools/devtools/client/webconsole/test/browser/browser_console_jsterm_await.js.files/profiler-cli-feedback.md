## Which marker a link's `marker=N` points at (review-browser_console_jsterm_await.js)

- Command: `profiler-cli load '<profiler link with ?marker=80237>' --session …`, then `profiler-cli marker info m-16..m-52 --json` piped through a script matching `markerIndex == 80237`.
- Expected: `load` of a link with `marker=N` to print that marker's handle (e.g. "Selected marker: m-50 ERROR …"), or a `marker info --index 80237`.
- Got: the load reports thread and range only; the link's marker is not surfaced, so checking a link means guessing the candidate handles and scripting over `--json`.
- Workaround: range `marker info` over the likely handles and filter on `markerIndex` in Python.
