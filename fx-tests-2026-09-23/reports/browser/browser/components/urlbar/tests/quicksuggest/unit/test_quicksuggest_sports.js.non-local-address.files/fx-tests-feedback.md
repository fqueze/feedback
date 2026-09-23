## Total runs for "Windows opt" across configs

- Question: "how many runs does this failure mode's population (Windows opt configs) have?"
- Command: `fx-tests test <path>` lists only failing configs with their runs; configs that ran without failing are not in that table.
- Workaround: summed the listed failing Windows opt configs by hand; did not run `--coverage` for the rest. A `--config windows --build opt` style aggregate (fails/runs for a config subset) would answer it.
