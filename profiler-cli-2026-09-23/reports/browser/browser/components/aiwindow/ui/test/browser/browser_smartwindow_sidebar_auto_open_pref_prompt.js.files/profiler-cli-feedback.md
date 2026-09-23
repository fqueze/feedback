## Question: how did the parent process's memory change, test by test?

Command: `profiler-cli thread markers --search "MEMORY STAT" --list --limit 0` on a resource-usage profile.
Expected: vsize / vsizeMaxContiguous per test, with the tests that made them jump.
Got: 337 text rows with the numbers inside the message, cut to the terminal width, so the test name is lost. Needed `--json` and a script that parses the message and prints the change per test.
What would have answered it: an option that turns a numeric field (or numbers in a message) into a column, and sorts by the change from the previous row.

## Question: machine memory and CPU over a time range, on a resource-usage profile

Command: `profiler-cli counter list` gives "No counters in this profile." Memory and CPU are `Memory` / `CPU Use` interval markers.
Expected: a summary of machine memory used and CPU % over the zoomed range, as `counter info` gives.
Got: one marker per 100 ms. Needed `--json` and a script to bucket them by second.
What would have answered it: `counter info`-style output (sparkline, per-bucket values) for numeric marker fields, or showing the resource-usage markers as counters.
