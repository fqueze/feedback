## Question: "how many ms between the test's exit and the leak warning?"

- Command: `profiler-cli thread markers --search test_webtransport_stop_sending --list --limit 0` (also after `zoom push m-1`, a 17.8 s range)
- Expected: times precise enough to order a test's log lines and see gaps of tens of ms.
- Got: every line from `Starting setup` to `Hit MOZ_CRASH` shows `t=6m43s`. The ordering and the gaps are invisible (exit at 403.364 s, Socket Thread warning at 403.432 s, Main Thread warning at 403.446 s, crash at 403.526 s).
- Workaround: `--json` plus a script that prints `start` in ms.
- What would have answered it: ms precision in `--list` when zoomed in, or always relative to the zoom start.

## Question: "was the machine saturated when the test crashed, compared with the rest of the parallel phase?"

- Commands: `profiler-cli counter list` gives `No counters in this profile.` `profiler-cli profile info` gives `CPU activity over time: No significant activity.`
- The resource-usage profile does have the data, as `CPU Use` markers every 100 ms with `idle_pct`.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` and a script counting samples with `idle_pct` <= 1% (99.1% of the parallel phase in Oow08).
- What would have answered it: a summary of one numeric marker field over the zoom (min / median / share below a threshold), or exposing these markers as a counter.
