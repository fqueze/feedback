## Question: in what order, to the millisecond, did these markers happen?

- Command: `profiler-cli thread markers --category Test --search browser_osAuthDialog --list --limit 0 --session <s>`
  (and the same with `--search 'AboutLogins:Setup,AboutLogins:SetBreaches,...'`)
- Expected: a start time precise enough to order markers that are milliseconds apart.
- Got: every row past one minute prints `t=1m1s` / `t=1m2s`, so the ~40 markers of the failing
  subtest (a 3 ms request/response, a prompt 1 ms after another message) all read `t=1m2s`, and
  their order and gaps cannot be read. `marker info` prints `Time: 1m2s` too.
- Workaround: `--json` piped through a python script printing `start/1000` with 3 decimals.
- What would have answered it: `t=62.029s`-style times in `--list` (seconds with ms) regardless of
  the profile's length, as `profile-link.py --range` and `zoom push` take them.
