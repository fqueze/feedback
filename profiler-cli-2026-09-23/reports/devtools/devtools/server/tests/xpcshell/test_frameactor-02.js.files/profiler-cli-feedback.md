## Marker times over one minute lose their milliseconds in `thread markers --list`

- Command: `profiler-cli thread markers --session test_frameactor-02.js-3 --search test_frameactor-02 --list --limit 0` (and `--search "Failed to launch"`), task ZtblArCLSx6O6KzfEkL1YQ resource-usage profile.
- Expected: start times precise enough to order two events a second apart, as below one minute (`t=46.478s`).
- Got: `t=1m5s` for the TIMEOUT and `t=1m4s` for the first launch failure; LKys: `t=2m13s` / `t=2m12s`. The order and gap between the launch failure and this test's queueing cannot be read.
- Workaround: none used; reported to 1 s resolution. `marker info` or `--json` would presumably give the exact time.

## Linking every marker a search matched needs `--json`

- Question: "a profile link for each marker matching `--search X` in this session".
- Command: `profiler-cli thread markers --session S --search test_frameactor-02 --list --limit 0 --json | jq -r '.flatMarkers[].handle'`, then `profile-link.py --marker` on each.
- Could have shown: a `--links` option on `thread markers --list` (or `profile-link.py --search`) printing the profiler URL next to each handle.
