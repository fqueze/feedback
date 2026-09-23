## Question: "which of these markers came first, to the 10 ms?" (ordering events within the same second)

- Command: `profiler-cli thread markers --session <s> --search "Detected crash,Killing background page,Failed to launch,will retry" --list --limit 0`
- Expected: start times precise enough to order events that happen within one second (e.g. `t=131.558s`).
- Got: past one minute, `--list` rounds the time column to whole seconds (`t=2m12s`) for every row, so a crash at 131.337 s, the first launch failure at 131.558 s and the next crash at 131.701 s all read `t=2m12s`. Rows are sorted, but nothing in the default output tells you how far apart they are.
- Workaround: `--json` piped through a python one-liner printing `start/1000`.
- What the output could show: millisecond precision in the time column (`2m11.558s`), at least when neighbouring rows share the same rounded second.

## Question: "what was the machine's CPU use during these seconds?" (CPU Use marker values in a zoom)

- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list` after `zoom push 129,133`
- Expected: each row showing its `cpuPercent` / `idle_pct`.
- Got: rows with no description (the payload fields aren't shown); `marker info` on each handle, through `--json` and a script, was the only way to get the numbers.
- What the output could show: the marker's main fields in the list row (as it already does for Text/Test markers), or a `profile cpu --range` summary for resource-usage profiles, which have no counters.

## Question: "which tests were still running at t=X?" (test markers' start and end, by name)

- Command: `profiler-cli thread markers --session <s> --search name:test --list --limit 0 --json` after `zoom push 55,63.95`, to compute start+duration for each test.
- Expected: each flat marker to carry the text shown in the plain list (`PASS — xpcshell-remote.toml:…/test_ext_background_early_shutdown.js`).
- Got: `description` is empty for these Test-schema markers in `--json`, so the script printed handles and times with no test names; I had to cross-reference the handles with the plain-text list.
- What the output could show: the same one-line description in `--json` as in the text list; or, directly, a `--running-at <t>` filter for interval markers ("which test markers span t=63.9 s"), which is the question here.
