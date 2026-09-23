## Marker times past 60 s print as `3m51s`

- Command: `profiler-cli thread markers --session <resource-usage profile> --search ... --list` inside a `zoom push 225,245`.
- Expected: times in seconds with milliseconds (`t=234.170s`), as for shorter profiles, so that TEST-* markers can be lined up with a per-test profile.
- Got: `t=3m50s`, `t=3m51s` for every marker in a 20 s window. Sub-second order is lost.
- Workaround: `--json` and a script that prints `start/1000`.

## Same issue, hit again in review (review-browser_settings_restore_from_backup.js)

- Command: `profiler-cli thread markers --session <MKA5 / Xfz7o resource-usage> --list --limit 0` inside `zoom push 288.6,289.5` and `zoom push 121.5,130.2`.
- Question: in what order, to the millisecond, did the hidden-node known-fail, the failure and `Filepicker shown` happen? Every row printed `t=4m49s`, or `t=2m1s`…`t=2m9s`.
- Workaround: `--json` and a script printing `start/1000`, again.
