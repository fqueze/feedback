## Question (review): in what order, to the ms, did the harness log lines of a 3 s window happen?

- Command: `profiler-cli zoom push 947.5,950.6 --session <s>` then `profiler-cli thread markers --list --limit 0 --session <s>`.
- Expected: start times precise enough to order a chmod, four ps listings and the failure message a few ms to 100 ms apart.
- Got: `t=15m48s` / `t=15m50s` on every row, so neither order nor gaps can be read. Workaround: `--json` and a script printing `start/1000` to 3 decimals.
- Could have shown: ms precision in `--list` when the view is under a minute (same as the entry in `test_census-tree-node-08.js.files/profiler-cli-feedback.md`).
