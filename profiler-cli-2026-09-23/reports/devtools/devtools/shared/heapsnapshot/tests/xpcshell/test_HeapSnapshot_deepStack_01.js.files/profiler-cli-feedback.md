## Question: which tests ended just before time t (who freed the slot this test took)?

- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0` (on an xpcshell resource-usage profile, zoomed around the test start).
- Expected: a way to see the end time of interval markers, or sort/filter by end (e.g. `--sort end`, or an `end` column), so "which tests finished in the 0.5 s before this one started" is one command.
- Got: start and duration only, sorted by start; a test that started 12 s earlier and ended 50 ms before mine is far up the list, and zooming to a window drops markers that started before it only if they do not overlap.
- Workaround: `--json` and a Python script computing `start + duration` and sorting by it.
