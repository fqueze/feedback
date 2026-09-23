## `--search` does not match the message of Log (mochitest INFO) markers

- Command: `profiler-cli thread markers --search "restore focus" --list --limit 0 --session test_bug333064.html-1` with t-12 (the mochi.test content GeckoMain) selected, profile `profile_test_bug333064.html.json` of task RvTP9NRFTcyoxaOUB4101w.
- Expected: the ~130 `INFO` markers whose message is "Error: Unable to restore focus, expect failures and timeouts." (the harness saying the window never had focus: the whole diagnosis).
- Got: `0 markers (filtered from 34315)`. The list rows for these markers print `[(empty)] INFO: (empty)`, and `marker info` prints `Message: (empty)`; `--json` has `"value": "Error: Unable to restore focus..."` with `"formattedValue": "(empty)"`. So both the formatter and the search miss the value.
- Workaround: listed every `INFO` handle, then `marker info <all handles> --json` and a Python one-liner to print `fields[].value`. Two extra round trips and a script for what should be one `--search`.

## `marker info --json` for several handles has no way to tell which record is which handle in the text view

- Minor: the JSON records do carry `markerHandle`, fine. Noting only that I first looked for `handle` (the key used by `thread markers --json` `topMarkers[]`), so the two outputs name the same thing differently.
- Command: `profiler-cli marker info m-347 m-348 --json --session ...` gives `markerHandle`; `profiler-cli thread markers --search X --json` gives `topMarkers[].handle`.
