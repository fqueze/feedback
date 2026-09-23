## Question: what was the machine's CPU use, per 100 ms, during one test's window?

- Command: `profiler-cli zoom push m-1 --session S; profiler-cli thread markers --session S --search 'name:CPU Use' --list --limit 0` on a resource-usage profile (profile_resource-usage.json of task ItvgBExkQnS1Ca0PFiOfBw).
- Expected: each row showing its cpuPercent (and idle %), the one payload value that matters for a CPU marker.
- Got: rows with only name, time and duration (`m-774 CPU Use t=17m7s 102ms`), no payload value at all.
- Workaround: `--json` and a Python one-liner printing `data.cpuPercent` per marker. The default row could show the marker's description/first fields as other marker types do.

## Question: which timer callback ran in which order, with sub-ms start times?

- Command: `profiler-cli thread markers --session S --search 'name:[anonymous]:JS' --list --limit 0`
- Expected: start times precise enough to order events that are 0.2 ms apart.
- Got: `t=1.051s`, `t=1.052s` ... rounded to the ms once past 1 s, so 40 runnables in 10 ms show only ~10 distinct times.
- Workaround: `--json` for `start`. The list could keep sub-ms precision (e.g. `t=1051.394ms`) when rows are closer together than the printed resolution.

## (review) Question: how long after one microtask checkpoint ended did the next timer runnable start?

- Command: `profiler-cli thread markers --session S --list --limit 0` over a 13 ms range of profile_test_FinderIterator.js (task ItvgBExkQnS1Ca0PFiOfBw).
- Expected: rows precise enough to see that a "Perform microtasks" ending at 1062.050 ms is followed by a Runnable at 1062.055 ms: an end time, or sub-ms starts.
- Got: `t=1.062s` starts and rounded durations. The 5 µs gap that decides the race cannot be read.
- Workaround: `--json` and a Python script that pairs each Runnable with the next "Perform microtasks" and prints start+duration. A `--show-end` column, or sub-ms precision when rows are close together, would answer it.

## (review) Question: which marker does a link's `marker=N` point to?

- Command: `profiler-cli marker info m-21 --session S`
- Expected: the marker's `markerIndex`, to check against the link's `marker=251423`.
- Got: no index in the text output; only `--json` has it.
- Workaround: `marker info <m> --json | python3 -c '...markerIndex'` for each link. Printing the index in the text output would do.
