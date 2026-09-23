## Question: "what was the status of test X's `test` marker" in an xpcshell resource-usage profile

- Command: `profiler-cli thread markers --search "early_shutdown" --list --session <s>` gives 425 markers (every log line of that test). The single `test` marker with its PASS/TIMEOUT status is buried among them. `--search` terms are OR'd by comma, so `name:test` and `early_shutdown` cannot be ANDed.
- Expected: some way to AND two terms (e.g. `name:test+early_shutdown`), or `--name <exact marker name>` combined with `--search`.
- Got: an OR, or a flood.
- Workaround: `--list --limit 0 --json` piped through a Python filter on `name == 'test'` and the test path.
