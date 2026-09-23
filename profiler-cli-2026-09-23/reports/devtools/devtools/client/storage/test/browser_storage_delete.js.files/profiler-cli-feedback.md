## Question: which of two markers in the same millisecond came first?

- Command: `profiler-cli thread markers --search "name:RDP Front,name:DevTools:RDP Front,..." --list --limit 0 --session browser_storage_delete.js-1`
- Expected: start times precise enough to order markers a few hundred microseconds apart (the order was the whole diagnosis: a request sent at 33546.000 ms vs an event received at 33546.443 ms).
- Got: `t=33.546s` for both, so the list could not say which came first; `marker info` without `--json` also rounds to ms.
- Workaround: `--json` and a Python script printing `start` with 3 decimals of ms. A `--precise`/µs time column in `--list` (or auto-extra digits when neighbouring rows share the same ms) would have answered it.

## Question: for every timer Runnable of a given name, which RDP requests started inside it?

- Command: `profiler-cli thread markers --search "debounce,name:RDP Front" --list --limit 0 --json` + script matching RDP Front starts against each Runnable's [start, start+duration].
- Expected: a way to list markers of type B nested in (started during) markers of type A across the whole profile, e.g. `--during-marker <search>` on `thread markers`, like the samples filter of the same name.
- Got: only per-marker `zoom push m-N` then list, which needs one round trip per candidate (131 debounce runnables here).
- Workaround: the script above. Also: flat markers in `--json` carry `duration` only for interval markers and no `end`, which is easy to misread as "no end".
