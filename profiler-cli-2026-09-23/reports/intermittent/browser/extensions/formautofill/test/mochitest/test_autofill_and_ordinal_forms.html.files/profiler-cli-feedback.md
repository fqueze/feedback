## Log markers (category Test, type Log) print "(empty)" for a non-empty message

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session test_autofill_and_ordinal_forms.html-1` on the content-process thread of a mochitest-plain per-test profile (task c5kEtvUAS3ioWjPiUNhfcA, `profile_test_autofill_and_ordinal_forms.html.json`), and `profiler-cli marker info m-28`.
- Expected: the INFO lines' text, e.g. `INFO: SimpleTest START`.
- Got: `[(empty)] INFO: (empty)` in the list, and `Level: (empty)`, `Message: (empty)` in `marker info`. `--json` shows `"value": "SimpleTest START"` with `"formattedValue": "(empty)"`: the formatter drops the value of these fields.
- Workaround: `marker info --json` and read `.fields[].value`.
