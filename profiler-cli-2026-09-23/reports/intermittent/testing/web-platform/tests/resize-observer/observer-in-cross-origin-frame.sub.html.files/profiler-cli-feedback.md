## Machine CPU during one test's interval in a resource-usage profile
- Question: what was the machine's CPU use while test X ran, and how does it compare with the rest of the job?
- Commands: `profiler-cli zoom push m-1` (the `test` marker), then `thread markers --search 'CPU Use' --list --limit 0`, then `marker info m-88..m-101` to read `CPU Percent`.
- Expected: the CPU percent per sample in the list, or a summary of it (min/median/max) for the zoomed range; `counter list` or `profile info` showing CPU.
- Got: `profile info` says "No significant activity" and `counter list` says "No counters" (CPU is stored as markers). The list shows no values, times are printed at 1 s resolution (`t=20m38s`) so rows in one second cannot be told apart, and the rows are not in handle order.
- Workaround: a Python script over `thread markers --search 'name:CPU Use' --list --limit 0 --json` that reads `fields[0].value`.
