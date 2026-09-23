## Machine CPU during one test (resource-usage profile)

Question: what was the machine's CPU use while test X ran (crash vs retry)?

- `profiler-cli thread markers --session 1994039-1 --search "name:CPU Use" --list --limit 0` (after `zoom push` on the test's range): expected the CPU percent per row; got only name, time and duration, the `cpuPercent` field is not in the list row. Workaround: `marker info` per handle, or `--json` piped to a script to read `fields[cpuPercent]`.
- What could have shown it: the CPU Use marker's cpuPercent in its `--list` row (as `test` markers show their status and path), or a one-line CPU summary in `thread markers` aggregate for the zoomed range.
