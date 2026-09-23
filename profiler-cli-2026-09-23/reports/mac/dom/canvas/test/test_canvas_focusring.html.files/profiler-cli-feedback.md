## Log markers print "(empty)" for fields that have a value

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session test_canvas_focusring.html-1` (thread t-18, content process of a mochitest-plain per-test profile), and `profiler-cli marker info m-3718 --session test_canvas_focusring.html-1`
- Expected: the harness's INFO lines, e.g. `INFO: must wait for focus`, `Error: Unable to restore focus, expect failures and timeouts.`
- Got: every `Log`-type marker renders as `[(empty)] INFO: (empty)`, and `marker info` shows `Level: (empty)` / `Message: (empty)`. With `--json`, `fields[].value` is `"must wait for focus"` while `fields[].formattedValue` is `"(empty)"`, so the text formatter reads `formattedValue` and the formatter for this schema produces nothing.
- Cost: the test's own log in a mochitest-plain profile (164 "Unable to restore focus" lines, the "must wait for focus" line) was invisible, and these were the key evidence. I only found them after seeing them in the resource-usage profile.
- Workaround: `--json`, plus a script over `flatMarkers[].data.message`.

## Question the default output did not answer: "which INFO messages did the test log, and how many of each"

- Command: `profiler-cli thread markers --category Test --search name:INFO --list --limit 0 --json --session test_canvas_focusring.html-1`, then a Python tally of `data.message`
- The default output could have answered it with a working message column (see above), or with `--group-by field:message` on Log markers.

## Default session directory not writable in a sandbox

- Command: `profiler-cli load <url> --session test_canvas_focusring.html-1`
- Got: `EPERM: operation not permitted, mkdir '/Users/florian/.profiler-cli'`. The error does suggest `PROFILER_CLI_SESSION_DIR`, which is good.
- Workaround: `PROFILER_CLI_SESSION_DIR=/Users/florian/.sandbox/tmp/profiler-cli` on every command, including `profile-link.py`, which reads the same variable.
