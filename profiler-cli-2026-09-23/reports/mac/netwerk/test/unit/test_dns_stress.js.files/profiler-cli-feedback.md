## `Error: Invalid string length` on a large filtered list

- Command: `profiler-cli thread markers --session dns1 --search name:Runnable,-DummyEvent --list --limit 0` (zoomed to a 30 s range of a profile with 1.9M Runnable markers)
- Expected: either the list, or a message that the result is too large and to add a filter / `--group-by`.
- Got: `Error: Invalid string length` (a JS RangeError leaking out), no hint. The bare `-DummyEvent` term was probably not an exclusion, so the filter matched ~1.9M markers.
- Workaround: `--search "name:Runnable,-name:DummyEvent,-name:TaskController::AddTask" --group-by field:name`.

## Question: how many markers matching X fell in each time window?

- Command: `profiler-cli thread markers --search DummyEvent --json` under nine successive `zoom push` ranges, parsed with python.
- Expected: a per-bucket count in the aggregate output (like `counter info`'s "over time" section) for a marker name.
- What its output could have shown: "DummyEvent: 17k/s before t=0.84s, 110-150k/s after".

## Question: was the machine CPU-saturated around the failure? (resource-usage profile)

- Command: `profiler-cli thread markers --session <ru> --search "name:CPU Use" --list --limit 0 --json`, then python to average `idle_pct` per 30 s bucket.
- Expected: the `CPU Use` markers summarised over time, the way `counter info` does for counters (the resource-usage profile has no counters, only these markers).
- What its output could have shown: "CPU busy 100% from 15m to 25m".

## Question: exact times of resource-usage markers, to align with a per-test profile

- Command: `profiler-cli thread markers --session <ru> --search test_dns_stress --list` prints `t=20m36s` (1 s resolution).
- Expected: millisecond times, as for short profiles (`t=786.35ms`), or an option for it.
- Workaround: `--json` and python to print `start` in ms.
