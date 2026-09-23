## `thread markers --list` times have no sub-second precision past one minute

- Command: `profiler-cli thread markers --category Test --search browser_smartwindow_prompts.js --list --limit 0 --session <s>` on a 10-minute per-test profile.
- Expected: times precise enough to order markers within a subtest (ms).
- Got: every row `t=9m55s`, so a spawn, the failure and a module evaluation 5 ms apart are indistinguishable; the question was "did the Spawn query arrive before ai-chat-content.mjs evaluated?".
- Workaround: `--json` and a python one-liner printing `start/1000` with 4 decimals, for every list.
## `marker info --json` returns a different shape for one handle and for several (review)

- Command: `profiler-cli marker info m-135 --json` vs `profiler-cli marker info m-135 m-160 --json`.
- Expected: one shape, so the same one-liner prints `markerIndex` whether a link has one marker to check or several.
- Got: a single `marker-info` object for one handle, and `{"type": "marker-info-multi", "markers": [...]}` for more. The question was "does this link's `marker=N` match the marker the report quotes?", and the one-liner written for one shape printed `None` for the other.
- Workaround: branch on `type`.
