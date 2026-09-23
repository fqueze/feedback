## `thread markers --list` prints very long marker payloads in full

- Command: `profiler-cli thread markers --session <s> --search longstring --list --limit 0` on a resource-usage profile
- Expected: one row per marker, the description cut to the terminal width.
- Got: a `console.log` marker's ~10,000-character string printed in full on one row (the test logs a long string), costing ~10k tokens for 4 markers.
- Workaround: pipe through `cut -c1-300`.
