## `thread samples` keeps using the last pushed zoom range after `zoom clear`

- Commands (profiler-cli 0.9.0, session on the Qc6fRMRTRDWsNccImVc8zQ per-test profile, thread t-0):
  `profiler-cli zoom push 15,20.3 --session S; profiler-cli zoom clear --session S; profiler-cli status --session S` (says "View range: Full profile"), then `profiler-cli thread samples --session S` and `... --include-idle`.
- Expected: counts for the full 2-minute profile (10,852 samples with idle).
- Got: "Categories (12 running samples)" and, with `--include-idle`, "517 running samples": the counts of the 15–20.3 s zoom. Same earlier on the worker thread of F4AEEb2t: after `zoom push 39.124,39.162` / `zoom clear`, `thread samples` on the whole thread reported "2 running samples", which I first read as the worker having almost no samples.
- Workaround: never rely on `zoom clear`; push the range wanted explicitly (`zoom push 0,121`) and check the sample count against the range length.

## Machine CPU during one test's run needs a script

- Question: "was the machine saturated while this test ran, and how did that compare with its retry?"
- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` on the resource-usage profile, then a Python script to keep the markers inside the `test` marker's range and compute idle %/system % median, min, max, and the count of 0%-idle samples.
- What could show it: `thread markers --search "CPU Use" --stats-field idle_pct` (or a summary of numeric fields per marker name) within the current zoom, and `zoom push m-N` on the `test` marker.
