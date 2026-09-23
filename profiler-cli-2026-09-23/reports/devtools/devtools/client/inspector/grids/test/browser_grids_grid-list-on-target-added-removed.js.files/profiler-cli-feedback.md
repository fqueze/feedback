## Question: "was the machine saturated between t1 and t2?" on a resource-usage profile

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session <ru>` after `zoom push 125.0,145.0`.
- Expected: one row per `CPU Use` sample showing its CPU / user / system / idle percentages (and `Memory used` for `Memory` markers), or `counter list` exposing them as counters with the "over time" buckets.
- Got: rows with a name, time and duration only; `counter list` says "No counters in this profile". The values are only in `--json` `fields[]`, so I had to script over the JSON to print CPU%, system% and memory per 100 ms.
- Workaround: `--json` + a Python loop over `flatMarkers[].fields`.
- What would have answered it: print the marker's label/fields in `--list` for `CPU`/`Mem`/`IO` payload types, or surface these resource-usage tracks as counters.

## Minor: zoom warning contradicts child-process data

- Command: `profiler-cli zoom push 55.0,62.5` on a per-test profile.
- Got: "Range extends outside the profile duration (61.563s)", while the example.org child process has markers up to 70.4 s (its IPCIn markers end at 70.2-70.4 s, `DllLoad xul.dll` ends at 66.7 s). Pushing a range past 61.563 s to look at them is legitimate.
