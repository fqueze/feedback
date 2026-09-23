## load reports a timeout, but the session works

- Command: `PROFILER_CLI_SESSION_OWNER=browser_markup_pseudo.js profiler-cli load "https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/avkaFxYkRI2Lliz-hLYJ1g/runs/0/artifacts/public/test_info/profile_browser_markup_pseudo.js.json" --session browser_markup_pseudo.js-1`
- Expected: either success, or a failure that leaves no usable session.
- Got: `Error: Profile load timeout after 60000ms (set PROFILER_CLI_LOAD_TIMEOUT_MS to override)`, yet the next `profile info --session ...` answered normally (the daemon kept loading). Ambiguous: a caller cannot tell whether to retry.
- Workaround: ignored the error and queried the session.

## Marker times printed at 1 s resolution in a long profile

- Question: in which order, within 50 ms, did the test's `INFO` log lines and the `DevToolsProcess` packets happen?
- Command: `profiler-cli thread markers --category Test --search browser_markup_pseudo.js --list --limit 0` and `marker info m-87 m-16`
- Got: every row `t=5m39s` / `t=5m40s` (profile is 5m45s long), so 40 consecutive markers had the same time; `marker info` also printed `Time: 5m39s`.
- Workaround: `--json` and a Python script printing `start` in ms. The list could print ms precision (e.g. `5m39.473s`), at least when neighbouring rows share the same rounded value, and `marker info` always should.

## (review) Ordering markers across two threads needed two `--json` dumps and a script

- Question: did the content process's `anonymousrootremoved` DOMEvents fire before or after its `domwalker:getMutations()` actor marker, and where does that fall relative to the parent's test log?
- Command: `profiler-cli zoom push 339.30,339.60`, then `thread markers --list --limit 0 --json` once on t-0 and once on t-114; `profile markers --search anonymousroot` also printed every row as `t=5m39s`.
- Got: both lists at 1 s resolution in the default output, so the order within 10 ms was only readable from `start` in the JSON.
- Could have shown: ms times in `--list` (same as the entry above), and a way to list markers of several threads merged in time order (e.g. `thread markers --thread t-0,t-114 --list`).
