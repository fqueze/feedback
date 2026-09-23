## "How many bytes went out and came back in each NetIO interval during test X?" (resource-usage profile)

- Command: `profiler-cli thread markers --search NetIO --list --limit 0` after `zoom push` on the test's window.
- Got: one row per NetIO marker with name and duration only; the Sent/Received fields needed `--json` and a script (reused `window.py` from the sibling report).
- Would help: `--list` printing the payload fields of the marker's schema (as `marker info` does), or a `--fields` option.
