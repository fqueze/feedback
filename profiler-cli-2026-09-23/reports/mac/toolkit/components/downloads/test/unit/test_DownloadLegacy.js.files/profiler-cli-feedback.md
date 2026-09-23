## `thread samples` after `zoom clear` kept returning the zoomed range

- Command: `profiler-cli zoom push 20.183,20.787 --session S`, then `profiler-cli zoom clear --session S`, then `profiler-cli thread samples --include-idle --session S` (profile_test_DownloadLegacy.js.json of task COszRY6cQ3eYMfxkW91UAg).
- Expected: the full profile's 681 samples.
- Got: "Categories (13 running samples)", the zoomed range's samples, while the header and `status` both said "View: Full profile". Repeating the `thread samples` call gave the same 13.
- Workaround: `zoom push 0,<end>` then `zoom clear` again; after that `thread samples` returned 681.

## Question: the machine's CPU use while one test ran

- Command: `profiler-cli zoom push m-1 --session S` (the `test` marker in profile_resource-usage.json), then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session S`.
- Expected: something that says whether the machine was saturated during that test, e.g. min/avg/max of the `CPU Percent` field over the zoomed range.
- Got: 419 rows of `CPU Use  t=6m31s  99ms`, with no field values in the list; `marker info` shows one marker's `CPU Percent` at a time.
- Workaround: `--json` and a Python script averaging `fields[cpuPercent]`. The aggregate view (`thread markers` without `--list`) could show per-field stats for numeric payload fields of the selected markers.

## Default session directory not writable in the sandbox

- Command: `PROFILER_CLI_SESSION_OWNER=test_DownloadLegacy.js profiler-cli load <taskcluster URL> --session test_DownloadLegacy.js-1`.
- Expected: the profile loads.
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error message itself suggested `PROFILER_CLI_SESSION_DIR`, which worked. One wasted load; `profile-link.py` already honours the variable.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every call. The subagent brief could mention it.
