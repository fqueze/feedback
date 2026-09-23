## Question: in what order, to the millisecond, did the harness's log lines happen around a failure?

- Command: `profiler-cli thread markers --session <s> --search "name:INFO,name:DEBUG,name:test" --list --limit 0` (resource-usage profile, 21 min long)
- Expected: timestamps precise enough to order lines that are 1 ms apart (a `ps` listing and the error logged right after it).
- Got: `t=3m29s` for every line in that second, so the list cannot say which came first. I had to use `--json` and read `start`.
- Also: a `get_process_list` DEBUG marker holds a 370-entry process list in one line, cut at the terminal width. Answering "was process X in the list" needed a script over `--json`. A `--search` that shows the matching part of a long field (a snippet around the match) would have answered it.
