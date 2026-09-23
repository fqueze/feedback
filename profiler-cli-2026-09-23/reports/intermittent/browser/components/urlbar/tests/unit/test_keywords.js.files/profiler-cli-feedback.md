## `thread markers --search ModuleEvaluation --list` shows no module name

- Command: `profiler-cli thread markers --session <s> --search ModuleEvaluation --list --limit 0`
- Expected: each row names the module being evaluated, as the `ChromeUtils.importESModule` rows name their URL.
- Got: rows with only the handle, time and duration (e.g. `m-127 ModuleEvaluation t=3.012s 3.950μs ✗`), so the list could not answer "which providers ran during this query".
- Workaround: `--search "name:ChromeUtils.importESModule"`, which carries the URL, and `marker stack` on those markers.
