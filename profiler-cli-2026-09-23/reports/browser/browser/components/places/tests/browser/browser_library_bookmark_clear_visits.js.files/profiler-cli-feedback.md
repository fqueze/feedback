## Question: which Library frame was on screen just after the TEST-UNEXPECTED-FAIL at 446.758 s, and was it before or after the failure?

- `profiler-cli screenshots --session <s> --range 446.70,447.33 -o <dir>` and `thread markers --list` print every time as `t=7m27s` (1 s resolution), so a screenshot cannot be ordered against a test marker in the same second without `--json`.
- With `--json`, the two commands disagree on the same marker's time: `marker info m-3938 --json` gives `start` 446806.88, while `screenshots --range … --json` lists m-3938 with `start` 446829.32. `screenshots --at 446.80 --json` reports `"at": 446822.4454` and `rootRange.start` 22.4454: the screenshots command adds the root range start (22.4 ms) to both its input and its output, and the marker commands do not.
- Expected: ms-resolution times in the text output of `screenshots` and `thread markers --list` (at least when several rows share a second), and the same time base across commands.
- Workaround: took `start` from `marker info --json` for both the test markers and the screenshot markers.
