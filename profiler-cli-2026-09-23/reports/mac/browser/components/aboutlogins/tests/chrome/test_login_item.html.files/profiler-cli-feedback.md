## `thread markers --list` hides the value of CPU Use markers

- Command: `profiler-cli thread markers --session tli-ru --search "name:CPU Use" --list --limit 0` on a resource-usage profile (task aXKirSbrT9GaPR8WK3sF2Q).
- Question: was the machine saturated while the test ran?
- Expected: each row shows its `cpuPercent` (and idle %), as the rows of other marker types show their text.
- Got: rows with only name, time and duration; the percentage is only in `--json` (`data.cpuPercent`), so a script was needed for a median/max over the range.
- Workaround: `--json | python3` over `flatMarkers[].data.cpuPercent`.
